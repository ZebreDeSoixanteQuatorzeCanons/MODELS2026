# γμS' Implementation Prompts

## AMULET EBNF Context-Free Grammar
```ebnf
AMULET command ::= modelOp | blockOp

modelOp ::= addBlock
          | delBlock
          | addLink
          | delLink
          | addConnection
          | delConnection
          | addContainment
          | delContainment

blockOp ::= addAttribute
          | delAttribute
          | addInputSignal
          | addOutputSignal
          | delSignal
          | addState
          | delState
          | addTransition
          | addActionOnSignal
          | delTransition
          | delActionOnSignal


addBlock ::= "add block" blockId
delBlock ::= "remove block" blockId


addLink ::= "add" [visibility] [linkMode] "link" [linkId] [fifoSpec] "between" blockId "and" blockId

delLink ::= "remove" [visibility] [linkMode] "link" [fifoSpec] "between" blockId "and" blockId ["and" blockName]
          | "remove link" linkId

visibility ::= "public" | "private"

linkMode ::= synchronousMode | asynchronousMode

synchronousMode ::= "synchronous" ["broadcast"]
asynchronousMode ::= "asynchronous" ["blocking"] ["lossy"]

fifoSpec ::= "with maxFIFO =" fifoSize


addConnection ::= "add connection from" signalId "in" blockId "to" signalId "in" blockId
                  ["in" [visibility] [linkMode] "link" [fifoSpec]]
                | "add connection from" signalId "to" signalId "in link" linkId

delConnection ::= "remove connection from" signalId "in" blockId "to" signalId "in" blockId
                  ["in" [visibility] [linkMode] "link" [fifoSpec]]
                | "remove connection from" signalId "to" signalId "in link" linkId


addContainment ::= "attach" blockId "to" blockId
delContainment ::= "detach" blockId ["from" blockId]


addAttribute ::= "add attribute" type attributeId ["=" intVal] "in" blockId
delAttribute ::= "remove attribute" attributeId "in" blockId

type ::= "int" | "bool"


addInputSignal ::= "add input signal" signalId [signalParams] "in" blockId
addOutputSignal ::= "add output signal" signalId [signalParams] "in" blockId
delSignal ::= "remove signal" signalId "in" blockId

signalParams ::= "(" paramDecl {"," paramDecl} ")"
paramDecl ::= type paramId
paramId ::= STRING


addState ::= "add state" stateId "in" blockId
delState ::= "remove state" stateId "in" blockId


addTransition ::= "add transition" [transitionId] "in" blockId "from" stateOrActionOnSignalId "to" stateOrActionOnSignalId [transitionSuffix]

delTransition ::= "remove transition in" blockId "from" stateOrActionOnSignalId "to" stateOrActionOnSignalId [transitionSuffix]
                | "remove transition" transitionId "in" blockId

transitionSuffix ::= "with" transitionBody

transitionBody ::= guardOnly
                 | afterOnly
                 | actionOnly
                 | guardAfter
                 | guardAction
                 | afterAction
                 | guardAfterAction

guardOnly ::= "[" guard "]"
afterOnly ::= "after(" intVal "," intVal ")"
actionOnly ::= "\"" actionExpression "\""

guardAfter ::= "[" guard "] and after(" intVal "," intVal ")"
guardAction ::= "[" guard "] and \"" actionExpression "\""
afterAction ::= "after(" intVal "," intVal ") and \"" actionExpression "\""
guardAfterAction ::= "[" guard "] and after(" intVal "," intVal ") and \"" actionExpression "\""


addActionOnSignal ::= "add action on signal" actionOnSignalId "in" blockId "with" actionOnSignalExpression

delActionOnSignal ::= "remove action on signal in" blockId "with" actionOnSignalExpression
                    | "remove action on signal" actionOnSignalId "in" blockId


stateOrActionOnSignalId ::= stateId | actionOnSignalId

blockId ::= STRING
linkId ::= STRING
blockName ::= STRING
signalId ::= STRING
attributeId ::= STRING
stateId ::= STRING
transitionId ::= STRING
actionOnSignalId ::= STRING

fifoSize ::= INTEGER
intVal ::= INTEGER

guard ::= BOOLEAN_EXPR
actionExpression ::= ASSIGNMENT | METHOD_CALL
actionOnSignalExpression ::= SIGNAL_CALL
```

This CFG was extracted and formatted by GPT-5.4 from the EBNF tables in the AMULET reference paper (https://dl.acm.org/doi/10.1145/3624583), based on our instructions to extract the raw EBNF.




## Stage 1 Prompts

- Specified task:
```
Identify suggestions using both the provided system specification and the model's description (in SysML v2). The suggestions should refer to important elements of the system specification that are not present in the model's description. A suggestion can also be to remove an element of the model that is not in the specification. Finally, a suggestion can be an element of the model that must be modified to better align with the specification. Propose at most [maxNbOfSuggestions] suggestions. You don't have to propose that many, you can propose less if the model is fine, or even no suggestion at all if the model is perfect!
```

- Output format constraints:
```
List the suggestions as a JSON array with the following elements for each suggestion: *name*: name of the suggestion; *type*: structure or behavior; *coherency*: true/false indicating whether it refers to a coherency issue; *description*: a precise description of the suggestion (e.g., missing element); *howToSolve*: how the model could be improved to address this suggestion; *reference*: name of the main concerned modeling element in the provided model (one word, no space); *outsiders*: elements that should be added to update the system (comma-separated); *views*: modeling views concerned by this suggestion (comma-separated), including the block diagram (use "BlockDiagram") and any relevant state machines (refer to a state machine using the corresponding block name). Respect this JSON format.
```

- MCP tools:
```
Also, to determine whether a suggestion is correct, use the MCP server tool "TTool", specifically the function "handle_pre_suggestion". Call this function at most three times. After at most three calls, make a prompt decision and return it.
```


## Stage 2 Prompts

- Specified task:
```
#TASK: translate the suggestion in a list of AMULET commands.
```

- Output format constraints:
```List the AMULET commands as a JSON array with the following element for each command: *command*: the command itself. Respect this JSON format, and ensure that all commands follow the AMULET syntax. Do not consider "part"; only add, remove, or modify "block", "state", "transition", "action on signal", "link", or "connection".
```
The EBNF CFG is also injected at this stage.

- MCP tools:
```
Also, to determine whether the suggestion is correct, use the MCP server tool "TTool", specifically the function "testingMutations". Call this function at most three times. After at most three calls, make a prompt decision and return it.
```


## Stage 3 Prompts

```
You are provided with a suggestion and a system to which this suggestion has been applied. Confirm that the new system, provided in SysML v2, implements this suggestion. Return only a score out of 10 (0 = no support for the suggestion, 10 = perfect support). Example: 8
```







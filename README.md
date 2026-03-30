# Welcome to γμS Companion Material!

This repository contains the material used to evaluate the TTool-based implementation of **γμS**, as submitted to MODELS '26.

## Repository Contents

- **`./models/`**
  This directory contains the models for the two case studies, including their base variants and the modified models obtained after applying the executable suggestions.

### Oceanographic Buoy

- **`oceanographicBuoy.xml`**
  This file gathers the two variants and the 10 modified models in a single `.xml` file. When opened in TTool, it contains the following tabs:

  - **`FullDesign`**: the initial complete design model, including one block diagram and 8 state machines.
  - **`OverspecifiedDesign`**: the first variant, which includes an additional `SatelliteTransmitter` block connected to the `Controller` block.
  - **`IncompleteDesign`**: the second variant, in which the `Barometer` block is missing.
  - For each *i* in [1; 5] and each *X* in {`Overspecified`, `Incomplete`}:
    - **`XDesign_Testi`**: the model produced for run *i* of γμS on `XDesign`. Each tab includes:
      - the computed suggestion,
      - the modified model,
      - the score assigned by LLM#3,
      - our own scores,
      - a short explanation of our evaluation.

### Coffee Machine

- **`CoffeeMachine_Series2.xml`**
  This file contains two variants and 10 modified models. When opened in TTool, it contains the following tabs:

  - **`AVATAR Requirements`**: a requirement diagram. It is not required for γμS.
  - **`FullDesign`**: the initial complete design model, including one block diagram and 4 state machines.
  - **`IncompleteDesign`**: the first variant, in which the `TeaButton` block is missing, and where the initial values of attributes and transitions are inconsistent with temporal constraints.
  - **`TimingErrors`**: the second variant, where the initial values of attributes and transitions are inconsistent with temporal constraints.
  - For each *i* in [1; 5] and each *X* in {`Completion`, `TimingErrors`}:
    - **`XDesign_Testi`**: the model produced for run *i* of γμS on `XDesign`. Each tab includes:
      - the computed suggestion,
      - the modified model,
      - the score assigned by LLM#3,
      - our own scores,
      - a short explanation of our evaluation.

- **`CoffeeMachine_Series3.xml`**
  This file contains one variant and 5 modified models. When opened in TTool, it contains the following tabs:

  - **`AVATAR Requirements`**: a requirement diagram. It is not required for γμS.
  - **`FullDesign`**: the third variant, which includes an additional `CappuccinoButton` block connected to the `CoffeeMachine` block.
  - For each *i* in [1; 5]:
    - **`Overspecification_Testi`**: the model produced for run *i* of γμS. Each tab includes:
      - the computed suggestion,
      - the modified model,
      - the score assigned by LLM#3,
      - our own scores,
      - a short explanation of our evaluation.



- **`./specifications/`**
  This directory provides the complete system specifications for both case studies, in Markdown.

- **`./evaluationResults/`**
  This directory contains an `.ods` spreadsheet gathering our detailed evaluation results for each of the 25 runs of γμS.

- **`./prompts/`**
  This directory contains a Markdown file gathering the predefined prompts used by our implementation to drive the three LLMs.


---

## Replication of the Results

### Installing and Configuring TTool

The following procedure has been validated on both **macOS** and **Linux** (Ubuntu 24.04.4 LTS). The following tools are required to install and run TTool: Gradle (7 or 8), `make`, `javac` (11), and `java` (11 or higher).

- First, install TTool by following the instructions provided [here](https://ttool.telecom-paris.fr/installation.html). We recommend installing it from the official [GitLab repository](https://gitlab.telecom-paris.fr/mbe-tools/TTool.git). Once cloned, compile TTool using:
  ```bash
  make ttool-cli && make ttoolnotest
  ```

- Next, configure AI support in TTool as described [here](https://ttool.telecom-paris.fr/ttoolai.html). We recommend using OpenAI Codex, which was used in our experiments.

- You must also configure TTool as an MCP server for Codex by following the instructions on the same page.

---

### Computing Suggestions with γμS

- Once TTool is installed, launch it:
  ```bash
  ./ttool.exe
  ```

- Open a model (e.g., **`CoffeeMachine_Series2.xml`**) and navigate to the **`IncompleteDesign`** tab. In the left panel, scroll to *`Suggestions by AI`*. Right-click on it and select **`Advanced suggestions for Avatar Design`** to start γμS.

- During execution, the console displays the interaction trace between TTool and the LLMs.

- When γμS completes, a suggestion appears in the tree. If the suggestion is *well-formed*, you can execute it by right-clicking and selecting **Apply on current model**.

---

### Known Limitation and Workaround

If the initial model contains syntax errors (in our dataset, this only affects the **Oceanographic Buoy** → **`IncompleteDesign`**), a regression recently introduced in our code currently prevents γμS from marking suggestions as well-formed. The walkthrough reported in the paper was obtained **before** this regression was introduced. This issue does not affect the reported evaluation results, since a workaround (used for Tests 2 to 5, with results equivalent to those of Test 1, see **`oceanographicBuoy.xml`**) still enables the suggestions to be executed:

1. In the terminal where TTool is running, copy the mutation commands displayed at the end of the suggestion computation.
2. Create a file in `TTool/bin/` (e.g., `mutation`) and paste the commands:
   - one command per line
   - remove trailing `;`
3. In the TTool GUI, run a syntax check on the faulty model.
4. In the terminal, execute:
   ```bash
   a amb mutation
   ```
   This invokes the AMULET compiler and applies the mutations to the model.

---

- After execution, the modified model is opened in a new tab.

---


Enjoy!

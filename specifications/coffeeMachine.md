#Coffee machine specification

```This coffee machine dispenses a beverage only after two coins have been deposited. If there's a substantial delay between (i.e., more than 30 seconds) the insertion of the first and second coins, the machine returns the initial coin. Likewise, if a beverage isn't selected promptly after the deposit of the two coins, both coins are automatically ejected. If either of the beverage buttons (tea or coffee) is pressed before the coins are ejected, the machine begins to prepare the selected drink. Notably, it takes 10 seconds to brew a coffee and 8 seconds to make a tea. Once the beverage has been collected, the machine is ready to accept new coins for the next order.

The user is represented as a Wallet that contains two coins when the system starts. The wallet takes between 20 and 40 seconds to insert a coin.

Use between 3 and 6 blocks.```
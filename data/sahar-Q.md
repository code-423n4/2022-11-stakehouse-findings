
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L56

This function need a modifier or require, So only authorized address (giant LP
l) can call it.

**************************************************************************************
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L36

Using “>=”  instead of “==” in this command is more logical. In some cases, maybe the user can't calculate an exact amount. 
**************************************************************************************

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L392

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L431


https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L155

Smaller variable can be used.

**************************************************************************************

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L239

Changing this important role should be done in two steps. First, the new address to get the role should be introduced, and then the new address claims the role.
**************************************************************************************

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L249

It is possible the DAO determine the amount of commission very high for its own benefit, so setting a range for determining the commission seems logical. (Especially MAX commission should be pre- defined.)
**************************************************************************************

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPToken.sol#L11

It may be necessary to change the address of the deployer in this contract in the future for any reason. This is an important role and there is a possibility of needing to change it. Therefore, in this agreement, there should be a two-step function where the current deployer can introduce another address as a new deployer role.
Then new address call a function and get role.

**************************************************************************************

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L71

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L72


Unlike the workflow of withdrawing Ether function from a contract, in this deposit function , it makes sense to add a new amount to the total supply just after a successful deposit operation.  
In other words, it is better to swap place of lines 71 and 72 together.
**************************************************************************************

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/SyndicateErrors.sol#L1

In this file, the pragma version is only set to “0.8.13”  , while in other files of this project, it is set to “^0.8.13” ( this version and above till 0.9.0). This version difference may cause an unknown problem during the compilation and deployment of the main contract on the mainnet.

**************************************************************************************

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L36


 ٌWallet may exist before calling this function (already cloned).
  Although calling and re-creating the wallet does not cause any problem, but  by checking the  condition [ if walletExists[wallet] == true]b before re-clone, you can avoid the re-execution of the function body and wasteful consumption of gas.
**************************************************************************************

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWallet.sol#L132

You can check the following condition [if (_isTransferApproved[from][to] != status)] and execute the next assignment command only if the condition is true, thus avoiding unnecessary gas consumptioan in some cases. For this, it is enough to swap the two lines number 132 and 133. 
**************************************************************************************

Thank you 🙂

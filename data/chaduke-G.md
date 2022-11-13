G1. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L673
function ``_initStakingFundsVault()`` is simple and only called once, it saves gas to inline this code into its caller. 

G2. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L674
function ``_initSavETHVault()`` is simple and only called once, it saves gas to inline this code into its caller. 

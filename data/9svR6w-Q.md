## 1 Ensure GiantSavETHVaultPool bringUnusedETHBackIntoGiantPool() receives eth properly and adds to idleETH

GiantSavETHVaultPool bringUnusedETHBackIntoGiantPool() is going to burn lp tokens
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L155

which will attempt to send eth back to GiantSavETHVaultPool at
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L189

It's not clear that GiantSavETHVaultPool has a function to receive this ETH. In addition, upon receiving the ETH it should update idleETH with this amount.



## 2 Ensure GiantMevAndFeesPool bringUnusedETHBackIntoGiantPool() receives eth properly and adds to idleETH

GiantMevAndFeesPool bringUnusedETHBackIntoGiantPool() is going to burn lp tokens
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L136

which will attempt to send eth back to GiantMevAndFeesPool at
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L190

It's not clear that GiantMevAndFeesPool has a function to receive this ETH. In addition, upon receiving the ETH it should update idleETH with this amount.


## 3 Consider calling updateAccumulatedETHPerLP() in GiantMevAndFeesPool _onWithdraw()

In GiantMevAndFeesPool _onWithdraw(), consider calling updateAccumulatedETHPerLP() before _distributeETHRewardsToUserForToken() so that the sender will be properly forwarded the rewards received as a result of the above calls to _lpTokens[i].transfer().

The updateAccumulatedETHPerLP() would go here:
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L186



## 4 Avoid calling ITransactionRouter.authorizeRepresentative to enable a zero representative in LiquidStakingManager

There is potentially a sequence of actions in which smart wallet does not have a representative or dormant representative. Then mintDerivatives() may attempt to call _authorizeRepresentative() with the zero dormant representative
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L618

Within _authorizeRepresentative() there would then be a call out to ITransactionRouter.authorizeRepresentative with the zero _eoaRepresentative set to enabled.

I don't have access to the contract source of the transaction router, but setting a value in this manner could potentially result in an error.
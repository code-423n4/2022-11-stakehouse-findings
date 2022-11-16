**contracts/liquid-staking/GiantPoolBase.sol**
- L31 - A variable is created in storage, liquidStakingDerivativeFactory, which is never used throughout the contract, therefore it has no reason to exist. In the case that it is necessary in another contract, it should be created there.

- L8 - LSDNFactory is imported, but it is only used to create a variable in storage that is never used, therefore it could be deleted.


**contracts/liquid-staking/SavETHVault.sol**
- L11 - The StakingFundsVault contract is imported, but it is never used, therefore it should be deleted as it is unnecessary.


**contracts/liquid-staking/GiantMevAndFeesPool.sol**
- L7/11 - The LPToken contract is imported twice, therefore one of the two should be deleted, since it is not necessary.


**contracts/liquid-staking/StakingFundsVault.sol**
- L6 - The IERC20 interface is imported, but it is never used, so it should be removed.

- L25/31/34 - Three events ETHDeposited, ERC20Recovered and WETHUnwrapped are created, but they are never used, therefore they should be removed.


**contracts/syndicate/Syndicate.sol**
- L15/17/27/45 - InvalidBLSPubKey, KnotSlashed and NotCollateralizedOwnerAtIndex errors are imported. Also CollateralizedSLOTReCalibrated events, but they are never used, so they should be removed.


**contracts/liquid-staking/SyndicateRewardsProcessor.sol**
- L43 - As the division that is performed to define newAccumulatedETH, an input (_numOfShares) is used, a validation should be performed with an if or a require to revert if a 0 is entered.


**contracts/liquid-staking/ETHPoolLPFactory.sol**
- L16 - The ETHWithdrawnByDepositor event is imported, but it is not used throughout the contract, so it must be removed.


**contracts/liquid-staking/LiquidStakingManager.sol**
- L6 - The Ownable class is imported, but it is not used throughout the contract, so it must be removed.


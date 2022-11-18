# QA

# Low

## Unresolved TODO that could lead to ether stuck
On [Syndicate.sol#L195](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L195) you have an unresolved todo that could lead to ETH stuck on the contract.


# Non critical


## Pragma too open
The codebase uses floating pragma. All contracts should be compiled with same pragma version. Locking the pragma ensures that contracts do not accidentally get deployed using either an outdated buggy compiler version or a compiler version different from what the code has been tested with.
Recommendation: use a fixed solc version on contracts, and open progma on interfaces.

Files:
- [OptionalGatekeeperFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol)
- [LPToken.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol)
- [GiantMevAndFeesPool.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol)
- [StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol)
- [SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)
- [OptionalHouseGatekeeper.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol)
- [SyndicateRewardsProcessor.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol)
- [LSDNFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol)
- [StakingFundsVaultDeployer.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol)
- [GiantLP.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol)
- [LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol)
- [ETHPoolLPFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol)
- [SavETHVaultDeployer.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol)
- [GiantSavETHVaultPool.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol)
- [LPTokenFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol)
- [GiantPoolBase.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol)
- [OwnableSmartWallet.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol)
- [OwnableSmartWalletFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol)

## Using draft version of Openzeppelin `ERC20PermitUpgradeable`
On [LPToken.sol#L6](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L6) you are using draft version of Openzeppelin `ERC20PermitUpgradeable`.
This contract is still a draft and is not considered ready for mainnet use. OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

## Missing a `__gap[50]` storage variable on `Syndicate` and `LpToken`
Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

Recommendation: add `__gap` variable:
```diff
+ uint256[50] __gap;
```

On this contracts;
- [LPToken.sol#L21](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L21)
- [Syndicate.sol#L121](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L121)

## Avoid magic numbers

On [LiquidStakingManager.sol#L870](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L870) instead of the magic number `(2 ** 256) - 1` use `typeof(uint256).max` for approving max;
Instead of
```
        sETH.approve(syndicate, (2 ** 256) - 1);
```
USe
```
        sETH.approve(syndicate, typeof(uint256).max);
```


## Un unused custom errors

Error `InvalidBLSPubKey` is not being in use;
[SyndicateErrors.sol#L8](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateErrors.sol#L8)
[Syndicate.sol#L15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L15)

Error `KnotSlashed` is not being in use;
[SyndicateErrors.sol#L10](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateErrors.sol#L10)
[Syndicate.sol#L17](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L17)

Error `NotCollateralizedOwnerAtIndex` is not being in use;
[SyndicateErrors.sol#L20](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateErrors.sol#L20)
[Syndicate.sol#L27](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L27)

## Events that arent emit
There are some events declared that are never emmited, you should remove them or emit them;
- Event `ETHDeposited` on [StakingFundsVault.sol#L25](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L25)
- Event `ERC20Recovered` on [StakingFundsVault.sol#L31](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L31)
- Event `WETHUnwrapped` on [StakingFundsVault.sol#L34](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L34)

## Format code using `forge fmt` or other tool

## Use format first license then pragma
Please review the solidity coding convention;
https://docs.soliditylang.org/en/v0.8.17/style-guide.html

The usually convention is license first, then pragma.

You could go with this on;
- [GiantLP.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L1-L3)
- [GiantMevAndFeesPool.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L1-L3)
- [GiantPoolBase.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L1-L3)
- [LPToken.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L1-L3)
- [LPTokenFactory.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L1-L3)
- [LSDNFactory.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L1-L3)
- [OptionalHouseGatekeeper.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L1-L3)
- [SavETHVaultDeployer.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L1-L3)
- [Syndicate.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L1-L3)
- [SyndicateErrors.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateErrors.sol#L1-L3)
- [SyndicateFactory.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L1-L3)
- [IGateKeeper.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/interfaces/IGateKeeper.sol#L1-L3)
- [ILPTokenInit.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/interfaces/ILPTokenInit.sol#L1-L3)
- [ILiquidStakingManager.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/interfaces/ILiquidStakingManager.sol#L1-L3)
- [ILiquidStakingManagerChildContract.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/interfaces/ILiquidStakingManagerChildContract.sol#L1-L3)
- [ISyndicateFactory.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/interfaces/ISyndicateFactory.sol#L1-L3)
- [ISyndicateInit.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/interfaces/ISyndicateInit.sol#L1-L3)
- [ITransferHookProcessor.sol#L1-L3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/interfaces/ITransferHookProcessor.sol#L1-L3)

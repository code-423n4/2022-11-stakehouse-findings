## 1. Event is missing `indexed` fields

_contracts/liquid-staking/GiantPoolBase.sol:_ [L19](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L19)

_contracts/liquid-staking/GiantSavETHVaultPool.sol:_ [L16](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L16)

_contracts/syndicate/Syndicate.sol:_ [L63](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L63)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L66](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L66)

_contracts/liquid-staking/StakingFundsVault.sol:_ [L28](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L28)
[L31](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L31)

_contracts/liquid-staking/SyndicateRewardsProcessor.sol:_ [L12](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L12)

_contracts/liquid-staking/SavETHVault.sol:_ [L22](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L22)
[L121](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L121)

_contracts/liquid-staking/ETHPoolLPFactory.sol:_ [L19](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L19)
[L22](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L22)
[L25](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L25)

## 2. Use `external` instead of `public` for the following functions

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L514](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L514)

_contracts/liquid-staking/GiantSavETHVaultPool.sol:_ [L29](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L29)

_contracts/liquid-staking/GiantMevAndFeesPool.sol:_ [L176](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L176)

_contracts/liquid-staking/LSDNFactory.sol:_ [L73](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L73)

_contracts/liquid-staking/SavETHVault.sol:_ [L200](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L200)

_contracts/liquid-staking/GiantPoolBase.sol:_ [L34](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L34)

_contracts/syndicate/SyndicateFactory.sol:_ [L21](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol#L21)

_contracts/liquid-staking/StakingFundsVault.sol:_ [L239](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L239)

_contracts/syndicate/Syndicate.sol:_ [L458](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L458)

## 3. Only libraries, abstract contracts and interfaces should use multiple compiler versions

_contracts/liquid-staking/OptionalGatekeeperFactory.sol:_ [L3](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L3)

_contracts/smart-wallet/OwnableSmartWalletFactory.sol:_ [L3](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L3)

_contracts/liquid-staking/SavETHVault.sol:_ [L3](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L3)

_contracts/liquid-staking/OptionalHouseGatekeeper.sol:_ [L1](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L1)

_contracts/smart-wallet/OwnableSmartWallet.sol:_ [L3](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L3)

_contracts/liquid-staking/GiantPoolBase.sol:_ [L1](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L1)

_contracts/liquid-staking/GiantMevAndFeesPool.sol:_ [L1](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L1)

_contracts/liquid-staking/LSDNFactory.sol:_ [L1](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L1)

_contracts/liquid-staking/GiantLP.sol:_ [L1](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L1)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L3](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L3)

_contracts/liquid-staking/ETHPoolLPFactory.sol:_ [L3](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L3)

_contracts/liquid-staking/SyndicateRewardsProcessor.sol:_ [L3](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L3)

_contracts/liquid-staking/LPToken.sol:_ [L1](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol#L1)

_contracts/liquid-staking/StakingFundsVault.sol:_ [L3](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L3)

_contracts/liquid-staking/GiantSavETHVaultPool.sol:_ [L1](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L1)

_contracts/liquid-staking/LPTokenFactory.sol:_ [L1](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPTokenFactory.sol#L1)

_contracts/liquid-staking/StakingFundsVaultDeployer.sol:_ [L3](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L3)

_contracts/liquid-staking/SavETHVaultDeployer.sol:_ [L1](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L1)

## 4. Do not leave the `receive`/`fallback` function empty

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L629](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L629)

_contracts/liquid-staking/SyndicateRewardsProcessor.sol:_ [L98](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98)

## 5. Events not emmited

_contracts/liquid-staking/SavETHVault.sol:_ [L235](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L235)

_contracts/liquid-staking/LPToken.sol:_ [L32](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol#L32)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L645](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L645)
[L904](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L904)
[L911](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L911)

_contracts/syndicate/Syndicate.sol:_ [L168](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L168)

_contracts/liquid-staking/StakingFundsVault.sol:_ [L371](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L371)

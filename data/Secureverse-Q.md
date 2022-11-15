### [LOW-01] 2 Diffenet Solidity version used

0.8.13 is used by followings contract
```solidity

File:         contracts/syndicate/Syndicate.sol
File:         contracts/syndicate/SyndicateErrors.sol
File:         contracts/syndicate/SyndicateFactory.sol

```

^0.8.13 is used by other(19) contracts


### [LOW-02] Floating Pragma used
Try to use recent updated and stable version of solidity
*Instances (19)*:
```solidity
Except 

File:         contracts/syndicate/Syndicate.sol
File:         contracts/syndicate/SyndicateErrors.sol
File:         contracts/syndicate/SyndicateFactory.sol

all other(19 contracts) using floating pragma

```

### [LOW-03] updateDAOAddress() should be a 2 step process

DAO address is a important state variable for this contract. During updation of this may be some human error can occur
So It should be a 2 step process.
first current Dao will set newDAOAddress to a variable and
in second step newDao address will call a function where it verifies whether its the same address or not

*Instances (1)*:
```solidity
File:         contracts/liquid-staking/LiquidStakingManager.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L239-L246

```
### [LOW-04] There should be a upperlimit for daoCommissionPercentage

*Instances (1)*:
```solidity
File:         contracts/liquid-staking/LiquidStakingManager.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L249-L252

```

### [LOW-05] Absence of zero address when setting critical state address variables

*Instances (2)*:
```solidity

File :           contracts/liquid-staking/OptionalHouseGatekeeper.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L15

```
```solidity
File:          contracts/liquid-staking/SavETHVault.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L45-L47
```


### [NC-01] License Identifier order mismatched
In Some contract files License Identifier defined at the top of contract and in some Identifier defined after pragma
*Instances (11)*:

Below contract files, where Identifier defined after pragma
```solidity

File:         contracts/syndicate/Syndicate.sol
File:         contracts/syndicate/SyndicateErrors.sol
File:         contracts/syndicate/SyndicateFactory.sol
File:         contracts/liquid-staking/GiantLP.sol
File:         contracts/liquid-staking/GiantMevAndFeesPool.sol
File:         contracts/liquid-staking/GiantPoolBase.sol
File:         contracts/liquid-staking/GiantSavETHVaultPool.sol
File:         contracts/liquid-staking/LPToken.sol
File:         contracts/liquid-staking/LPTokenFactory.sol
File:         contracts/liquid-staking/LSDNFactory.sol
File:         contracts/liquid-staking/SavETHVaultDeployer.sol

```


### [NC-02] Magic Number used
Instead of magic number try to use constant or immutable state variable

*Instances (33)*:
```solidity
File:         contracts/syndicate/Syndicate.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L221
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L223
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L429
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L431
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L504
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L522
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L546

```
```solidity
File:           contracts/liquid-staking/ETHPoolLPFactory.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L82
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L83
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L88
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L89
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L112

```
```solidity
File:           contracts/liquid-staking/GiantMevAndFeesPool.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L116
```
```solidity
File:           contracts/liquid-staking/GiantPoolBase.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L96
```
```solidity
File:           contracts/liquid-staking/GiantSavETHVaultPool.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L127
```
```solidity
File:         contracts/liquid-staking/LiquidStakingManager.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L333
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L334
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L749
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L752
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L766
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L882
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L936
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L940
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L944
```

```solidity
File:          contracts/liquid-staking/SavETHVault.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L244
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L204
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L174
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L161
```

```solidity
File:       contracts/liquid-staking/StakingFundsVault.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L58
```
```solidity
File:       contracts/liquid-staking/StakingFundsVault.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L184
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L230
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L240
```







### [NC-03] Return data from a low level call is not checked

Should be a require() check on return data of low level call

*Instances (15)*:
```solidity
File:         contracts/syndicate/Syndicate.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L321-L322
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L668-L669
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L

```
```solidity
File:         contracts/smart-wallet/OwnableSmartWallet.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L78-L79

```
```solidity
File:           contracts/liquid-staking/GiantPoolBase.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L61
```
```solidity
File:         contracts/liquid-staking/LiquidStakingManager.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L412
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L415
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L460
```
```solidity
File:          contracts/liquid-staking/SavETHVault.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L209
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L189
```

```solidity
File:       contracts/liquid-staking/StakingFundsVault.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L190
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L244
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L380
```
```solidity
File:       contracts/liquid-staking/SyndicateRewardsProcessor.sol

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L67
```

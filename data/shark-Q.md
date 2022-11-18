## Use `immutable` variable

State variables that have no setter functions and can only be assigned at the constructor can be declared `immutable`.

Here is an example:

File: `OptionalHouseGatekeeper.sol` [Line 14-16](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L14-L16)

```
    constructor(address _manager) {
        liquidStakingManager = ILiquidStakingManager(_manager);
    }
```

The variable above could be changed to immutable like so:

```
    ILiquidStakingManager public immutable liquidStakingManager;

    constructor(address _manager) {
        liquidStakingManager = ILiquidStakingManager(_manager);
    }
```

Here are all the other instances of this issue:

File: `SavETHVaultDeployer.sol` [Line 13-16](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L13-L16)
File: `StakingFundsVaultDeployer.sol` [Line 13-16](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L13-L16)
File: `LPTokenFactory.sol` [Line 15-22](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15-L22)
File: `LPTokenFactory.sol` [Line 15-22](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15-L22)
File: `SyndicateFactory.sol` [Line 13-18](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L13-L18)
File: `LSDNFactory.sol` [Line 15-68](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L15-L68)
File: `GiantLP.sol` [Line 11-27](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L11-L27)

## Empty event

File: `Syndicate.sol` [Line 39](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L39)

The following event is not emitting anything. Consider either refactoring to be of use or removing it.

```
    event ContractDeployed();
```

## Unspecific Pragma Version

e.g. `pragma solidity ^0.8.13;` is very unspecific.

Locking the pragma helps ensure that contracts don't get deployed with unintended versions, for example, the latest compiler which could have higher risks of undiscovered bugs.

## Typo mistakes

File: `ETHPoolLPFactory.sol` [Line 74](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L74), [Line 118](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L118), [Line 124](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L124), [Line 150](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L150)

```
/// @audit Instane
74:    /// @param _newLPToken Instane of the new LP token (to be minted)

// Replace "it's" to "its"
118:            // KNOT and it's LP token is already registered


///@audit depoister
124:            // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied

///@audit depoister
150:            // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied

```

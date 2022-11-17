## Low

### Code comment suggests two checks should be performed, but only one check is performed

[Location](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L468)

#### Description

The code inside the function `registerBLSPublicKeys()` contains the following comment: 

> // check if the BLS public key is part of LSD network and is not banned

However, the `require` statement immediately following the comment only contains a check for the BLS public key existence check, and not the additional key banned check.

#### Suggested course of action

Update the comment if the check isn't required, or add the check the comment indicates should be present.

## Low

### Function is misleadingly named

[Location](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L500)

#### Description

The function `isBLSPublicKeyBanned()` performs two different checks, only one of which is indicated in its name. It does perform a BLS key banned check. It also performs a BLS key registration check. Its later use in `stake()` and `mintDerivatives()` is such that both checks are required, via code comments. This gives the appearance of mistaken code comments.

#### Suggested course of action

Rename the function to be consistent with its implementation and use.

## Low

### Revert string contradicts implemented check

[Location](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L940)

#### Description

At the indicated line, the `require` statement contains the following revert string:

> "DAO staking funds vault balance must be at least 4 ether"

However, the actual check is a strict equality for `4 ether`, and not _at least_ `4 ether`.

#### Suggested course of action

Change the revert string to correlate with the actual performed check.

## Low

### Missing length check on array inconsistent with other functions

[Location](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L28)

#### Description

The function `GiantMevAndFeesPool.batchDepositETHForStaking()` lacks an array length check that other functions in similar contracts do.

Specifically, the check on the array length of `_ETHTransactionAmounts` compared to `_stakingFundsVault` is missing. The mirrored function in `GiantSavETHVaultPool` contains this check, as do nearly all other functions that contain multiple array type arguments.

#### Suggested course of action

Add in the missing check to be consistent with other functions in the protocol.

## Low

### Users can leave amounts of ETH in contracts inheriting GiantPoolBase when using `withdrawETH()`

[Location](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L53)

#### Description

The `GiantPoolBase` contract provides baseline functions for other contract which inherit it, including `GiantMevAndFeesPool` and `GiantSavETHVaultPool`. 

The function `withdrawETH()` is used to burn LP tokens and retrieve ETH from the pool. It contains several checks, among which is the following:

```
//MIN_STAKING_AMOUNT = 0.001 ether
require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
```

Since the contract enforces a minimum deposit, this check seems to be attempting to match the logic on deposit.

However, the logic as implemented permits the user to leave a value of less than the `MIN_STAKING_AMOUNT` in the contract.

For example, Alice wishes to participate in the `GiantMevAndFeesPool` to earn a portion of fees. She deposits 0.01 ether, well above the minimum amount.

Later, she wishes to withdraw, and calls `withdrawETH` with a value of `9500000000000000`, corresponding to `0.0095 ether`. This quantity will pass the `require` check as it is larger than `MIN_STAKING_AMOUNT`, and Alice's ETH will be withdrawn (assuming the other conditions are met, such as the quantity of idleETH being greater than or equal to her requested amount).

This will leave 0.0005 ETH in the pool. If Alice tries to withdraw the dust, the transaction will fail. It will force her to deposit ETH and perform the withdrawal a second time.

#### Suggested course of Action

Consider changing the logic to check if the difference of the requested amount would leave less than `MIN_STAKING_AMOUNT` in the pool. If so, simply transfer the user's entire balance instead of leaving dust. 

Alternatively, perform the same test but revert and require an amount that wouldn't violate `MIN_STAKING_AMOUNT` afterwards.


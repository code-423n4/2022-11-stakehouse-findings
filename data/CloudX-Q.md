# Q1

### array length is not validated.

At `batchDepositETHForStaking()` method, `_ETHTransactionAmounts` parameter's length is not validated to be equals to the others. It does not cause any balance or create any inconsistencies in the protocol but will raise out of bound exception and revert the transaction of course.

So validate it as equals you do with the other parameters:
`require(numOfVaults == _ETHTransactionAmounts.length, "Inconsistent lengths");`
Include the change after this line:
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L37

---
---

# Q2

### array length is not validated.

Similar than the previous issue but in this case the amounts array and the tokens is assumed to have the same length.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L83-L84

The following validation could be added at line 80:
require(_lpTokens[i].length == _amounts[i].length, "Inconsistent amounts and tokens arrays");
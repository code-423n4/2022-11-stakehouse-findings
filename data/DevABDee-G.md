### 1. `MODULO` can be defined as a constant variable to save some gas.
[LiquidStakingManager.sol#L158](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L158).

### 2. Use `Custom Errors` instead of `Require` with string error messages for better gas optimization.
So So Many `require`s are defined
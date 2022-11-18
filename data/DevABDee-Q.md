### 1. DRY code. Use Modifiers 

Two Same and only Require statements are used in two functions, here [GiantLP.sol#L30](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L30) and here [GiantLP.sol#L35](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L35).

Better make a `onlyPool` modifier

### 2. `Pragma` not fixed

Floating pragmas are considered a bad practice.
It is always recommended that pragma should be fixed (not floating) to the version that you are intending to deploy your contracts with.
Fix the `Pragma` to `0.8.13` in every .sol file
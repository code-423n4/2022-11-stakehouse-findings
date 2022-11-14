## Table of Contents

- [L-01] Transfer of Ownership should be a two-step process
- [N-01] if( should be if ( to match other lines in the file
- [N-02] Use neutral terms instead of sensitive ones
- [N-03] Lock floating pragma for non-library / interface contracts
- [N-04] Open Todos
- [N-05] 2**<N>-1 should be re-written as type(uint<n>).max


### [L-01] Transfer of Ownership should be a two-step process

When transferring DAO contract, make sure the process is two-step to prevent accidental transfer errors

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L239-L246

###  [N-01] if( should be if ( to match other lines in the file

For standardization purposes

for(
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L392
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L465

for (
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L538
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L587

###  [N-02] Use neutral terms instead of sensitive ones

Use allowlist rather than whitelist

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L265-L284

### [N-03] Lock floating pragma for non-library / interface contracts

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L3

### [N-04] Open Todos
Code is not closed properly

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L195

### [N-05] 2**<N>-1 should be re-written as type(uint<n>).max

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L870
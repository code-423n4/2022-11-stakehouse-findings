There is one missing for [GAS-3]	Using bools for storage incurs overhead.
Use uint256(1) and uint256(2) for true/false. It can save gas, and won't increase storage size in struct.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L34-L37


MODULO is currently declared as a state variable. Should be declared as a private constant. This will save gas.
Furthermore MODULO value can be assigned as 1e7, instead of 100_00000 for the ease of readability.

After change it will look as follows:
uint256 private MODULO = 1e7;

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158
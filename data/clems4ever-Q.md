# Bug in the withdraw function of GiantMevAndFeesPool

A user can deposit ethereum to GiantMevAndFeesPool but cannot withdraw apparently.

POC: https://gist.github.com/clems4ever/64ae725c7288ba8f48d22cdd5e5e0f0c

Just run the POC in the test suite.

# Publicly available updateAccumulatedETHPerLP function

Do those functions need to be publicly available?

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/StakingFundsVault.sol#L62
- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/GiantMevAndFeesPool.sol#L141

They update the state so I'd rather not let them open if I were you. I've not found direct ways to trigger an exploit from calling them directly but I've found indirect ways to call them in order to trigger a bug anyway, I think this function should be at least protected from being called by anyone just for the sake of peace of mind.
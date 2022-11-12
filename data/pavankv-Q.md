1. Lock the pragma and update to > 0.8.14 

Description :-
Because 0.8.13 have several bugs that are all fixed in latest version and above 0.8.14 version try noticed overflows and underflows instead of using safeMath Library  .

code snippet:-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L1
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L3

Reference :-
https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/

##  [G1]  INCREMENTS and DECREMENTS CAN BE UNCHECKED

### In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

> There are  instances of this issue:


               2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

               63:    for (uint256 i; i < numOfRotations; ++i) {

               144    numberOfLPTokensIssued++;

               2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

               38:     for (uint256 i; i < numOfVaults; ++i) {

###

## [G2]  

                      
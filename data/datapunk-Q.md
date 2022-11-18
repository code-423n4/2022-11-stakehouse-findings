## N1. dividing before multiplication
The way claims are calculated, it is dividing by _numOfShares first, then multiply by _balanceOfSende:
`uint256 newAccumulatedETH = accumulatedETHPerLPShare + ((unprocessed * PRECISION) / _numOfShares);`
`((newAccumulatedETH * _balanceOfSender) / PRECISION)`
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#43
However, since PRECISION is present, I am not sure how much the impact is.
Just fruit for thought to potentially refactor the formula

## N2. ETH not accumulated in `previewAccumulatedETH`
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L91 supposed to have
`accumulated += ...`
Although it is an external view function, depending on its usages, it may present more issues to the callers.

## N3. in `_addPriorityStakers`, duplicated stakers are pass the dup check
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L588 checks
`if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();`
while it should check
`if (i > 0 && staker <= _priorityStakers[i-1]) revert DuplicateArrayElements();`

## N4. incorrect comment
`require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");`
should be:
`require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must have 4 ether");`
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L940

## N5. redundant calls to `updateAccumulatedETHPerLP`
in `StakingFundsVault`, the last thing `_claimFundsFromSyndicateForDistribution` does is to call `updateAccumulatedETHPerLP`, yet everywhere `_claimFundsFromSyndicateForDistribution`  is called, it is followed by `updateAccumulatedETHPerLP`. The 2nd does nothing, so can be removed.

## N6. `unstake` in `Syndicate` does not check for 0 amount.
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L259
There is no minimum value for _sETHAmount to be unstaked.
When 0 amount is supplied, it should be checked and skipped early.

## N7. not checking against address(0)
Add `require(_pool != address(0))` to
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L25

Add `require(_liquidStakingManager != address(0))` to
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L12

Add `require(_manager != address(0))` to
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L14

## N8. wrong error message
[`if (_recipient == address(this)) revert ZeroAddress(); `](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L296)
[`if (getSlotRegistry().currentSlashedAmountOfSLOTForKnot(blsPubKey) != 0) revert InvalidNumberOfCollateralizedOwners();`](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L575)


## N9. make `contract GiantPoolBase` abstract
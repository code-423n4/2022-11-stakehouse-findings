## 2**<N> - 1 SHOULD BE RE-WRITTEN AS TYPE(UINT<N>).MAX

Instead of `2**32` write `type(uint32).max`
Instead of `2**256` write `type(uint256).max`

1 Instance: 
-LiquidStakingManager.sol line 870


# RECEIVE() FUNCTION WILL LOCK ETHER IN CONTRACT
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

2 Instances:
-LiquidStakingManager.sol line 629
-SyndicateRewardsProcessor.sol line 98








### Inline a function that’s only used once
It costs less gas to inline the code of functions that are only called once. Doing so saves the cost of allocating the function selector, and the cost of the jump when the function is called.
___
[Exchange.sol: L684-692](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L684-L692)
```solidity
    function _isNodeRunnerValid(address _nodeRunner) internal view returns (bool) {
        require(_nodeRunner != address(0), "Zero address");

        if(enableWhitelisting) {
            require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
        }

        return true;
    }
```
`function _isNodeRunnerValid()` above is called only once (below):

[Exchange.sol: L436](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L436)
```solidity
        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
```

Similarly for the following `functions`:

[Exchange.sol: L739-745](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L739-L745)

[Exchange.sol: L776-780](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L776-L780)

[Exchange.sol: L816-820](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L816-L820)

[Exchange.sol: L904-908](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L904-L908)

[Exchange.sol: L911-918](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L911-L918)

[Exchange.sol: L921-931](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L921-L931)

[Exchange.sol: L934-945](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L934-L945)
___
___


### Avoid use of '&&' within a `require` function
Splitting into separate `require()` statements saves gas
___
[LiquidStakingManager.sol: L357](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L357)
```solidity
        require(_new != address(0) && _current != _new, "New is zero or current");
```
Recommendation:
```solidity
        require(_new != address(0), "New is zero");
        require(_current != _new, "New is current");
```
___
___


### Avoid use of default "checked" behavior in `for` loops
Underflow/overflow checks are made every time `++i` (or `++j` or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. 

All of the `for` loops in `Stakehouse` employ default "checked" behavior. Using `unchecked {++i}` for each `for` loop (as shown in the example below) would save gas.
___
[GiantSavETHVaultPool.sol: L128-130](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L128-L130)
```solidity
        for (uint256 i; i < numOfRotations; ++i) {
            SavETHVault(_savETHVaults[i]).batchRotateLPTokens(_oldLPTokens[i], _newLPTokens[i], _amounts[i]);
        }
```
Suggestion:
```solidity
        for (uint256 i; i < numOfRotations;) {
            SavETHVault(_savETHVaults[i]).batchRotateLPTokens(_oldLPTokens[i], _newLPTokens[i], _amounts[i]);

            unchecked {
              ++i;
          }
        }
```
___
___

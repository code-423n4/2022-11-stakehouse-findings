### GiantMevAndFeesPool `batchRotateLPTokens()` - documentation typo

Function   comment lists `@param _oldLPTokens` twice (second should be `_newLPTokens`).

### GiantSavETHVaultPool.sol `batchRotateLPTokens()` - documentation typo

function comment for batchRotateLPTokens() lists  lists `@param _oldLPTokens` twice (second should be `_newLPTokens`).

### LPToken - inconsistancy in @dev comment

mint() has a dev notice 
```
  /// @notice Mints a given amount of LP tokens
    /// @dev Only savETH vault can mint
    function mint(address _recipient, uint256 _amount) external onlyDeployer {
        _mint(_recipient, _amount);
    }
```
But burn() has similar implementation (via onlyDeployer modifier), but no similar notice.

### LSDNFactory.sol - function comments missing half of the params
For consistency its usually better to add comments describing all params, or none at all. The first and last param are documented, but middle two are not. This, like the other param comments, may be intentional though

### LiquidStakingManager.sol - function comments missing last param documentation
For consistency its usually better to add comments describing all params, or none at all. The  last param is the only not documented. This, like the other param comments, may be intentional though

### Syndicate.sol missing function comment param notes
The `claimAsCollateralizedSLOTOwner()` function has 2 params, but only one is documented. This, like the other param comments, may be intentional though

### Syndicate.sol inaccurate comments

- The comment for `calculateETHForFreeFloatingOrCollateralizedHolders()` starts off saying "Using `highestSeenBalance`", but this does not seem to be the case. I think the comment is outdated. 
- The ordering of the params in `previewUnclaimedETHAsFreeFloatingStaker` are in the wrong order. Not a huge issue, but something quick to change and make a bit neater.



### Unused events 
These could either be a QA issue (no need to have unused events), or a gas issue (cheaper deployment if they're removed)
- **StakingFundsVault.sol**:
	- `ETHDeposited`
	- `ERC20Recovered`
	- `WETHUnwrapped`
- **Syndicate.sol**
	- `CollateralizedSLOTReCalibrated`

### Importing from files with different solidity versions

`LiquidStakingManager` imports from `IOwnableSmartWalletFactory.sol`, which has a different Solidity version. Not a huge deal but not recommended.

### Unused imports in `Syndicate.sol`

Several imports are not used:
- InvalidBLSPubKey
- KnotSlashed
- NotCollateralizedOwnerAtIndex

### Unused internal functions in `Syndicate.sol`

Some unused internal functions.
- `_calculateNewAccumulatedETHPerCollateralizedShare()``
- ``

### Syndicate.sol has a TODO 

**updateAccruedETHPerShares()** still has a TODO to finish up.

`// todo - check else case for any ETH lost`



### Syndicate.sol check for duplicate address in _addPriorityStakers()
```
if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();
```

This reverts with an error called DuplicateArrayElements. But it does not revert if its a duplicate of the last one in the `_priorityStakers` array... it only reverts if the address is less than the previous staker address.
It does not check the entire array for duplicates.
Initially I thought they want to check `isPriorityStaker[staker]` is not set, but its clearly not as they have the `i > 0` check.
If that is the intention: then `if(!isPriorityStaker[staker])  revert  revert DuplicateArrayElements()`
The current implementation would also require the array be sorted, but nothing indicates that. If that is needed, it should be indicated (maybe by a comment)
# Report
## Gas Optimizations ##
### [G-1]: The increment in for loop post condition can be made unchecked
**Context:**

1. ```for (uint256 i; i < amountOfTokens; ++i) {``` [L76](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L76) 
1. ```for (uint256 i; i < numOfSavETHVaults; ++i) {``` [L42](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L42) 
1. ```for (uint256 i; i < numOfVaults; ++i) {``` [L78](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L78) 
1. ```for (uint256 j; j < _lpTokens[i].length; ++j) {``` [L82](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L82) 
1. ```for (uint256 i; i < numOfRotations; ++i) {``` [L128](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L128) 
1. ```for (uint256 i; i < numOfVaults; ++i) {``` [L146](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L146) 
1. ```for (uint256 j; j < _lpTokens[i].length; ++j) {``` [L148](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L148) 
1. ```for (uint256 i; i < numOfValidators; ++i) {``` [L63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L63) 
1. ```for (uint256 i; i < numOfTokens; ++i) {``` [L103](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L103) 
1. ```for (uint256 i; i < numOfTokens; ++i) {``` [L116](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L116) 
1. ```for (uint256 i; i < numOfVaults; ++i) {``` [L38](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L38) 
1. ```for (uint256 i; i < numOfVaults; ++i) {``` [L64](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L64) 
1. ```for (uint256 i; i < _stakingFundsVaults.length; ++i) {``` [L90](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L90) 
1. ```for (uint256 i; i < numOfRotations; ++i) {``` [L117](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L117) 
1. ```for (uint256 i; i < numOfVaults; ++i) {``` [L135](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L135) 
1. ```for (uint256 i; i < _lpTokens.length; ++i) {``` [L183](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L183) 
1. ```for (uint256 i; i < numOfValidators; ++i) {``` [L78](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L78) 
1. ```for (uint256 i; i < numOfTokens; ++i) {``` [L152](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L152) 
1. ```for (uint256 i; i < numOfTokens; ++i) {``` [L166](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L166) 
1. ```for (uint256 i; i < _blsPubKeys.length; ++i) {``` [L203](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L203) 
1. ```for (uint256 i; i < _blsPublicKeys.length; ++i) {``` [L266](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L266) 
1. ```for (uint256 i; i < _blsPubKeys.length; ++i) {``` [L276](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L276) 
1. ```for (uint256 i; i < _token.length; ++i) {``` [L286](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L286) 
1. ```for (uint256 i; i < _blsPubKeys.length; ++i) {``` [L211](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L211) 
1. ```for (uint256 i; i < _blsPubKeys.length; ++i) {``` [L259](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L259) 
1. ```for (uint256 i; i < _blsPubKeys.length; ++i) {``` [L301](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L301) 
1. ```for (uint256 i; i < _blsPubKeys.length; ++i) {``` [L346](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L346) 
1. ```for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {``` [L420](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L420) 
1. ```for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {``` [L513](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L513) 
1. ```for (uint256 i; i < knotsToRegister; ++i) {``` [L560](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L560) 
1. ```for (uint256 i; i < _priorityStakers.length; ++i) {``` [L585](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L585) 
1. ```for (uint256 i; i < _blsPublicKeys.length; ++i) {``` [L598](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L598) 
1. ```for (uint256 i; i < _blsPubKeys.length; ++i) {``` [L648](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L648) 
1. ```for(uint256 i; i < _blsPubKeys.length; ++i) {``` [L392](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L392) 
1. ```for(uint256 i; i < len; ++i) {``` [L465](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L465) 
1. ```for (uint256 i; i < numOfValidators; ++i) {``` [L538](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L538) 
1. ```for (uint256 i; i < numOfKnotsToProcess; ++i) {``` [L587](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L587) 
1. ```for (uint256 i; i < numOfRotations; ++i) {``` [L63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L63) 

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Example how to fix. Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-2]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

```idleETH -= _amount;``` [L57](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L57) 

**Description:**

Some gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.

### [G-3]: If internal function called only once it can be inlined to save gas
**Context:**

```function _assertUserHasEnoughGiantLPToClaimVaultLP(LPToken _token, uint256 _amount) internal view {``` [L93](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L93) 

**Description:**

If [they](https://blog.soliditylang.org/2021/03/02/saving-gas-with-simple-inliner/) are not inlined, they cost an additional 20 to 40 gas because of 2 extra jump instructions and additional stack operations needed for function calls.

### [G-4]: Cache a value from a mapping/array in local memory 
**Context:**

1. https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L500-L535 (isNoLongerPartOfSyndicate[_blsPubKey])
```
        if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {
            ...
        }

        // if the knot is no longer active, no further accrual of rewards are possible snapshots are possible but ETH accrued up to that point
        // Basically, under a rage quit or voluntary withdrawal from the beacon chain, the knot kick is auto-propagated to syndicate
        if (!isActive && !isNoLongerPartOfSyndicate[_blsPubKey]) {
            _deRegisterKnot(_blsPubKey);
        }
```

2. https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L296-L299 (smartWalletRepresentative[smartWallet])
```
        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

        // unauthorize old representative
        _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);
```

3. https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L314-L317 (smartWalletRepresentative[smartWallet])
```
        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

        // unauthorize old representative
        _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);
```

4. https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L454-L455 (smartWalletRepresentative[smartWallet])
```
        if(smartWalletRepresentative[smartWallet] != address(0)) {
            require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
```

**Description:**

If you read value from mapping/array more than once within a function then it is cheaper to cache it in local memory and then read it from memory wnen it is neaded. This will save about 40 gas.

**Recommendation:**

Example how to fix. Change:
```
        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

        // unauthorize old representative
        _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);
```

to:
```
        address _oldRepresentative = smartWalletRepresentative[smartWallet];
        require(_oldRepresentative != _newRepresentative, "Invalid rotation to same EOA");

        // unauthorize old representative
        _authorizeRepresentative(smartWallet, _oldRepresentative, false);
```

### [G-5]: Cache state variable in memory and not re-read it from the storage
**Context:**

1. https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L177-L189 (numberOfRegisteredKnots)
```
        if (numberOfRegisteredKnots > 0) {
            ...
            }
            uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);
```

2. https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L241-L243 (dao)
```
        require(_newAddress != dao, "Same address");

        emit UpdateDAOAddress(dao, _newAddress);
```

3. https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L361-L371 (dao)
```
        require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");

        ...

        if (msg.sender == dao && _wasPreviousNodeRunnerMalicious) {
```

**Description:**

Every reading from storage costs about 100 gas while every reading from memory costs only 3 gas. If state variable referred more than once within a function then it is cheaper to cache it in local memory (100 gas) and then read it from memory wnen it is neaded (3 gas) rather than read state variable from storage everytime (100 gas).

**Recommendation:**

Example how to fix. Change:
```
        require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");

        ...

        if (msg.sender == dao && _wasPreviousNodeRunnerMalicious) {
```

to:
```
        address _dao = dao;
        require(_current == msg.sender || _dao == msg.sender, "Not current owner or DAO");

        ...

        if (msg.sender == _dao && _wasPreviousNodeRunnerMalicious) {
```

### [G-6]: Use calldata instead of memory
**Context:**

1. ```function execute(address target, bytes memory callData)``` [L41](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L41) 
1. ```bytes memory callData,``` [L54](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L54) 
1. ```bytes memory callData,``` [L69](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L69) 
1. ```function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {``` [L355](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L355) 
1. ```function _claimFundsFromSyndicateForDistribution(address _syndicate, bytes[] memory _blsPubKeys) internal {``` [L360](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L360) 
1. ```function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {``` [L337](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L337) 
1. ```function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {``` [L343](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L343) 
1. ```function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {``` [L359](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L359) 
1. ```address[] memory _priorityStakers,``` [L472](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L472) 
1. ```bytes[] memory _blsPubKeysForSyndicateKnots``` [L473](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L473) 
1. ```function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {``` [L491](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L491) 
1. ```function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots) internal {``` [L555](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L555) 
1. ```function _addPriorityStakers(address[] memory _priorityStakers) internal {``` [L583](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L583) 
1. ```function _deRegisterKnot(bytes memory _blsPublicKey) internal {``` [L610](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L610) 
1. ```function _autoStakeWithSyndicate(address _associatedSmartWallet, bytes memory _blsPubKey) internal {``` [L879](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L879) 

**Description:**

If a reference type function parameter is read-only, it is recommended to use calldata instead of memory because this provides significant gas savings. Since Solidity v0.6.9, memory and calldata are allowed in all functions regardless of their visibility type (ie external, public, etc).

### [G-7]: Don't compare boolean expressions to boolean literals
**Context:**

1. ```vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,``` [L150](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L150) 
1. ```require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");``` [L64](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L64) 
1. ```require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");``` [L84](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L84) 
1. ```require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");``` [L79](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79) 
1. ```require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");``` [L114](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114) 
1. ```liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,``` [L205](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L205) 
1. ```if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();``` [L611](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L611) 
1. ```if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();``` [L612](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L612) 
1. ```require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");``` [L291](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L291) 
1. ```require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");``` [L328](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L328) 
1. ```require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");``` [L332](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L332) 
1. ```require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");``` [L393](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L393) 
1. ```require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");``` [L436](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L436) 
1. ```require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");``` [L437](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L437) 
1. ```require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");``` [L469](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L469) 
1. ```require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");``` [L541](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L541) 
1. ```require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");``` [L589](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L589) 
1. ```require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");``` [L688](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L688) 


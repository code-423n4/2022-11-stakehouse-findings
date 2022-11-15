# LSD Network - Stakehouse Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (11 instances)
2. Cache `<array>.length` (16 instances)
3. Use `unchecked{}` to suppress overflow/underflow check (38 instances)
4. Long `require()`/`revert()` string (36 instances)
5. Using `bool`s for storage incurs overhead (11 instances)
6. Use `!= 0` instead of `> 0` when comparing uint (37 instances)
7. Empty blocks should be removed or emit something (10 instances)
8. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (1 instance)
9. Use `abi.encodePacked()` instead of `abi.encode()` (1 instance)
10. Use `private` instead of `public` for constants (4 instances)
11. Don't compare boolean expressions to boolean literals (2 instances)
12. Use custom errors instead of `revert()`/`require()` strings (202 instances)
13. Use shift right/left instead of division/multiplication if possible (2 instances)
14. Functions guaranteed to revert when called by normal users can be marked `payable` (15 instances)

Total 386 instances of 14 issues.

## 1. Use `calldata` instead of `memory` (11 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
contracts/smart-wallet/OwnableSmartWallet.sol::41 => function execute(address target, bytes memory callData)

contracts/liquid-staking/StakingFundsVault.sol::355 => function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {

contracts/liquid-staking/StakingFundsVault.sol::360 => function _claimFundsFromSyndicateForDistribution(address _syndicate, bytes[] memory _blsPubKeys) internal {

contracts/syndicate/Syndicate.sol::337 => function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {

contracts/syndicate/Syndicate.sol::343 => function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {

contracts/syndicate/Syndicate.sol::359 => function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {

contracts/syndicate/Syndicate.sol::491 => function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {

contracts/syndicate/Syndicate.sol::555 => function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots) internal {

contracts/syndicate/Syndicate.sol::583 => function _addPriorityStakers(address[] memory _priorityStakers) internal {

contracts/syndicate/Syndicate.sol::610 => function _deRegisterKnot(bytes memory _blsPublicKey) internal {

contracts/liquid-staking/LiquidStakingManager.sol::879 => function _autoStakeWithSyndicate(address _associatedSmartWallet, bytes memory _blsPubKey) internal {
```

## 2. Cache `<array>.length` (16 instances)

If `<array>.length` is used as for loop termination condition, then the `.length` method will be called in each iteration. Caching it in a local variable can save gas.

```solidity
contracts/liquid-staking/GiantSavETHVaultPool.sol::82 => for (uint256 j; j < _lpTokens[i].length; ++j) {

contracts/liquid-staking/GiantSavETHVaultPool.sol::148 => for (uint256 j; j < _lpTokens[i].length; ++j) {

contracts/liquid-staking/GiantMevAndFeesPool.sol::90 => for (uint256 i; i < _stakingFundsVaults.length; ++i) {

contracts/liquid-staking/GiantMevAndFeesPool.sol::183 => for (uint256 i; i < _lpTokens.length; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::203 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::266 => for (uint256 i; i < _blsPublicKeys.length; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::276 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::286 => for (uint256 i; i < _token.length; ++i) {

contracts/syndicate/Syndicate.sol::211 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::259 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::301 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::346 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::585 => for (uint256 i; i < _priorityStakers.length; ++i) {

contracts/syndicate/Syndicate.sol::598 => for (uint256 i; i < _blsPublicKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::648 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/liquid-staking/LiquidStakingManager.sol::392 => for(uint256 i; i < _blsPubKeys.length; ++i) {
```

## 3. Use `unchecked{}` to suppress overflow/underflow check (38 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
contracts/liquid-staking/GiantPoolBase.sol::76 => for (uint256 i; i < amountOfTokens; ++i) {

contracts/liquid-staking/GiantSavETHVaultPool.sol::42 => for (uint256 i; i < numOfSavETHVaults; ++i) {

contracts/liquid-staking/GiantSavETHVaultPool.sol::78 => for (uint256 i; i < numOfVaults; ++i) {

contracts/liquid-staking/GiantSavETHVaultPool.sol::82 => for (uint256 j; j < _lpTokens[i].length; ++j) {

contracts/liquid-staking/GiantSavETHVaultPool.sol::128 => for (uint256 i; i < numOfRotations; ++i) {

contracts/liquid-staking/GiantSavETHVaultPool.sol::146 => for (uint256 i; i < numOfVaults; ++i) {

contracts/liquid-staking/GiantSavETHVaultPool.sol::148 => for (uint256 j; j < _lpTokens[i].length; ++j) {

contracts/liquid-staking/SavETHVault.sol::63 => for (uint256 i; i < numOfValidators; ++i) {

contracts/liquid-staking/SavETHVault.sol::103 => for (uint256 i; i < numOfTokens; ++i) {

contracts/liquid-staking/SavETHVault.sol::116 => for (uint256 i; i < numOfTokens; ++i) {

contracts/liquid-staking/GiantMevAndFeesPool.sol::38 => for (uint256 i; i < numOfVaults; ++i) {

contracts/liquid-staking/GiantMevAndFeesPool.sol::64 => for (uint256 i; i < numOfVaults; ++i) {

contracts/liquid-staking/GiantMevAndFeesPool.sol::90 => for (uint256 i; i < _stakingFundsVaults.length; ++i) {

contracts/liquid-staking/GiantMevAndFeesPool.sol::117 => for (uint256 i; i < numOfRotations; ++i) {

contracts/liquid-staking/GiantMevAndFeesPool.sol::135 => for (uint256 i; i < numOfVaults; ++i) {

contracts/liquid-staking/GiantMevAndFeesPool.sol::183 => for (uint256 i; i < _lpTokens.length; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::78 => for (uint256 i; i < numOfValidators; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::152 => for (uint256 i; i < numOfTokens; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::166 => for (uint256 i; i < numOfTokens; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::203 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::266 => for (uint256 i; i < _blsPublicKeys.length; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::276 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/liquid-staking/StakingFundsVault.sol::286 => for (uint256 i; i < _token.length; ++i) {

contracts/syndicate/Syndicate.sol::211 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::259 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::301 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::346 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::420 => for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

contracts/syndicate/Syndicate.sol::513 => for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

contracts/syndicate/Syndicate.sol::560 => for (uint256 i; i < knotsToRegister; ++i) {

contracts/syndicate/Syndicate.sol::585 => for (uint256 i; i < _priorityStakers.length; ++i) {

contracts/syndicate/Syndicate.sol::598 => for (uint256 i; i < _blsPublicKeys.length; ++i) {

contracts/syndicate/Syndicate.sol::648 => for (uint256 i; i < _blsPubKeys.length; ++i) {

contracts/liquid-staking/LiquidStakingManager.sol::392 => for(uint256 i; i < _blsPubKeys.length; ++i) {

contracts/liquid-staking/LiquidStakingManager.sol::465 => for(uint256 i; i < len; ++i) {

contracts/liquid-staking/LiquidStakingManager.sol::538 => for (uint256 i; i < numOfValidators; ++i) {

contracts/liquid-staking/LiquidStakingManager.sol::587 => for (uint256 i; i < numOfKnotsToProcess; ++i) {

contracts/liquid-staking/ETHPoolLPFactory.sol::63 => for (uint256 i; i < numOfRotations; ++i) {
```

## 4. Long `require()`/`revert()` string (36 instances)

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

```solidity
contracts/liquid-staking/GiantPoolBase.sol::55 => require(idleETH >= _amount, "Come back later or withdraw less ETH");

contracts/liquid-staking/SavETHVault.sol::64 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

contracts/liquid-staking/SavETHVault.sol::84 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/SavETHVault.sol::90 => require(msg.value == _amount, "Must provide correct amount of ETH");

contracts/liquid-staking/SavETHVault.sol::204 => require(_amount >= 24 ether, "Amount cannot be less than 24 ether");

contracts/liquid-staking/StakingFundsVault.sol::79 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

contracts/liquid-staking/StakingFundsVault.sol::114 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/StakingFundsVault.sol::120 => require(msg.value == _amount, "Must provide correct amount of ETH");

contracts/liquid-staking/StakingFundsVault.sol::154 => require(address(token) != address(0), "No ETH staked for specified BLS key");

contracts/liquid-staking/StakingFundsVault.sol::240 => require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

contracts/liquid-staking/StakingFundsVault.sol::267 => require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");

contracts/liquid-staking/LiquidStakingManager.sol::256 => require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

contracts/liquid-staking/LiquidStakingManager.sol::257 => require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

contracts/liquid-staking/LiquidStakingManager.sol::258 => require(numberOfKnots == 0, "Cannot change ticker once house is created");

contracts/liquid-staking/LiquidStakingManager.sol::268 => require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");

contracts/liquid-staking/LiquidStakingManager.sol::280 => require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

contracts/liquid-staking/LiquidStakingManager.sol::291 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::328 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::331 => require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");

contracts/liquid-staking/LiquidStakingManager.sol::332 => require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::393 => require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::396 => require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

contracts/liquid-staking/LiquidStakingManager.sol::435 => require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");

contracts/liquid-staking/LiquidStakingManager.sol::437 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::455 => require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");

contracts/liquid-staking/LiquidStakingManager.sol::469 => require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::541 => require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::589 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::662 => require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");

contracts/liquid-staking/LiquidStakingManager.sol::663 => require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

contracts/liquid-staking/LiquidStakingManager.sol::936 => require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

contracts/liquid-staking/LiquidStakingManager.sol::939 => require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");

contracts/liquid-staking/LiquidStakingManager.sol::940 => require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

contracts/liquid-staking/LiquidStakingManager.sol::944 => require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");

contracts/liquid-staking/ETHPoolLPFactory.sol::122 => require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

contracts/liquid-staking/ETHPoolLPFactory.sol::130 => require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

## 5. Using `bool`s for storage incurs overhead (11 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
contracts/liquid-staking/LSDNFactory.sol::76 => bool _deployOptionalHouseGatekeeper,

contracts/smart-wallet/OwnableSmartWallet.sol::129 => bool status

contracts/smart-wallet/OwnableSmartWallet.sol::131 => bool statusChanged = _isTransferApproved[from][to] != status;

contracts/liquid-staking/SavETHVault.sol::36 => bool withdrawn;

contracts/liquid-staking/SavETHVault.sol::141 => bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;

contracts/syndicate/Syndicate.sol::233 => bool transferResult = sETH.transferFrom(msg.sender, address(this), _sETHAmount);

contracts/syndicate/Syndicate.sol::275 => bool transferResult = sETH.transfer(_sETHRecipient, _sETHAmount);

contracts/liquid-staking/LiquidStakingManager.sol::120 => bool public enableWhitelisting;

contracts/liquid-staking/LiquidStakingManager.sol::179 => bool _deployOptionalGatekeeper,

contracts/liquid-staking/LiquidStakingManager.sol::655 => bool _deployOptionalGatekeeper,

contracts/liquid-staking/LiquidStakingManager.sol::698 => bool _isEnabled
```

## 6. Use `!= 0` instead of `> 0` when comparing uint (37 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
contracts/liquid-staking/GiantPoolBase.sol::71 => require(amountOfTokens > 0, "Empty arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::36 => require(numOfSavETHVaults > 0, "Empty arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::72 => require(numOfVaults > 0, "Empty arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::123 => require(numOfRotations > 0, "Empty arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::143 => require(numOfVaults > 0, "Empty arrays");

contracts/liquid-staking/SavETHVault.sol::59 => require(numOfValidators > 0, "Empty arrays");

contracts/liquid-staking/SavETHVault.sol::101 => require(numOfTokens > 0, "Empty arrays");

contracts/liquid-staking/SavETHVault.sol::114 => require(numOfTokens > 0, "Empty arrays");

contracts/liquid-staking/GiantMevAndFeesPool.sol::35 => require(numOfVaults > 0, "Zero vaults");

contracts/liquid-staking/GiantMevAndFeesPool.sol::62 => require(numOfVaults > 0, "Empty array");

contracts/liquid-staking/GiantMevAndFeesPool.sol::112 => require(numOfRotations > 0, "Empty arrays");

contracts/liquid-staking/GiantMevAndFeesPool.sol::132 => require(numOfVaults > 0, "Empty arrays");

contracts/liquid-staking/StakingFundsVault.sol::71 => require(numOfValidators > 0, "Empty arrays");

contracts/liquid-staking/StakingFundsVault.sol::150 => require(numOfTokens > 0, "Empty arrays");

contracts/liquid-staking/StakingFundsVault.sol::164 => require(numOfTokens > 0, "Empty arrays");

contracts/liquid-staking/StakingFundsVault.sol::320 => require(blsPubKey.length > 0, "Invalid token");

contracts/liquid-staking/StakingFundsVault.sol::346 => require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");

contracts/syndicate/Syndicate.sol::177 => if (numberOfRegisteredKnots > 0) {

contracts/syndicate/Syndicate.sol::183 => if (totalFreeFloatingShares > 0) {

contracts/syndicate/Syndicate.sol::315 => if (unclaimedUserShare > 0) {

contracts/syndicate/Syndicate.sol::500 => if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {

contracts/syndicate/Syndicate.sol::551 => return totalFreeFloatingShares > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingShares : 0;

contracts/syndicate/Syndicate.sol::588 => if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();

contracts/syndicate/Syndicate.sol::634 => lastAccumulatedETHPerFreeFloatingShare[_blsPublicKey] > 0 ?

contracts/syndicate/Syndicate.sol::656 => if (unclaimedUserShare > 0) {

contracts/liquid-staking/LiquidStakingManager.sol::386 => require(_blsPubKeys.length > 0, "No BLS keys specified");

contracts/liquid-staking/LiquidStakingManager.sol::414 => if (daoAmount > 0) {

contracts/liquid-staking/LiquidStakingManager.sol::532 => require(numOfValidators > 0, "No data");

contracts/liquid-staking/LiquidStakingManager.sol::583 => require(numOfKnotsToProcess > 0, "Empty array");

contracts/liquid-staking/LiquidStakingManager.sol::922 => require(_received > 0, "Nothing received");

contracts/liquid-staking/LiquidStakingManager.sol::924 => if (daoCommissionPercentage > 0) {

contracts/liquid-staking/SyndicateRewardsProcessor.sol::37 => if (_balanceOfSender > 0) {

contracts/liquid-staking/SyndicateRewardsProcessor.sol::59 => if (balance > 0) {

contracts/liquid-staking/SyndicateRewardsProcessor.sol::62 => if (due > 0) {

contracts/liquid-staking/SyndicateRewardsProcessor.sol::77 => if (_numOfShares > 0) {

contracts/liquid-staking/SyndicateRewardsProcessor.sol::81 => if (unprocessed > 0) {

contracts/liquid-staking/ETHPoolLPFactory.sol::59 => require(numOfRotations > 0, "Empty arrays");
```

## 7. Empty blocks should be removed or emit something (10 instances)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
contracts/liquid-staking/LPToken.sol::28 => constructor() initializer {}

contracts/liquid-staking/GiantPoolBase.sol::100 => function _onDepositETH() internal virtual {}

contracts/liquid-staking/GiantPoolBase.sol::103 => function _onWithdraw(LPToken[] calldata _lpTokens) internal virtual {}

contracts/smart-wallet/OwnableSmartWallet.sol::25 => constructor() initializer {}

contracts/liquid-staking/SavETHVault.sol::43 => constructor() initializer {}

contracts/liquid-staking/StakingFundsVault.sol::43 => constructor() initializer {}

contracts/syndicate/Syndicate.sol::123 => constructor() initializer {}

contracts/liquid-staking/LiquidStakingManager.sol::166 => constructor() initializer {}

contracts/liquid-staking/LiquidStakingManager.sol::629 => receive() external payable {}

contracts/liquid-staking/SyndicateRewardsProcessor.sol::98 => receive() external payable {}
```

## 8. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (1 instance)

Instead of using operator && on single require check, using double `require()` checks can save more gas.

```solidity
contracts/liquid-staking/LiquidStakingManager.sol::357 => require(_new != address(0) && _current != _new, "New is zero or current");
```

## 9. Use `abi.encodePacked()` instead of `abi.encode()` (1 instance)

`abi.encodePacked()` is more efficient than `abi.encode()`.

```solidity
contracts/syndicate/SyndicateFactory.sol::63 => return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));
```

## 10. Use `private` instead of `public` for constants (4 instances)

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

```solidity
contracts/liquid-staking/GiantPoolBase.sol::22 => uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;

contracts/syndicate/Syndicate.sol::66 => uint256 public constant PRECISION = 1e24;

contracts/liquid-staking/SyndicateRewardsProcessor.sol::15 => uint256 public constant PRECISION = 1e24;

contracts/liquid-staking/ETHPoolLPFactory.sol::38 => uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
```

## 11. Don't compare boolean expressions to boolean literals (2 instances)

`if (<x> == true)` can be refactored to `if (<x>)`, `if (<x> == false)` can be refactored to `if (!<x>)`.

```solidity
contracts/syndicate/Syndicate.sol::611 => if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();

contracts/syndicate/Syndicate.sol::612 => if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
```

## 12. Use custom errors instead of `revert()`/`require()` strings (202 instances)

Using `require()`/`revert()` strings is expensive. Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors.

Custom errors decrease both deploy and runtime gas costs. Note that runtime gas cost is only relevant when the revert condition is met.

```solidity
contracts/liquid-staking/LPTokenFactory.sol::19 => require(_lpTokenImplementation != address(0), "Address cannot be zero");

contracts/liquid-staking/LPTokenFactory.sol::33 => require(address(_deployer) != address(0), "Zero address");

contracts/liquid-staking/LPTokenFactory.sol::34 => require(bytes(_tokenSymbol).length != 0, "Symbol cannot be zero");

contracts/liquid-staking/LPTokenFactory.sol::35 => require(bytes(_tokenName).length != 0, "Name cannot be zero");

contracts/liquid-staking/GiantLP.sol::30 => require(msg.sender == pool, "Only pool");

contracts/liquid-staking/GiantLP.sol::35 => require(msg.sender == pool, "Only pool");

contracts/liquid-staking/LPToken.sol::23 => require(msg.sender == deployer, "Only savETH vault");

contracts/liquid-staking/GiantPoolBase.sol::35 => require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");

contracts/liquid-staking/GiantPoolBase.sol::36 => require(msg.value == _amount, "Value equal to amount");

contracts/liquid-staking/GiantPoolBase.sol::53 => require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

contracts/liquid-staking/GiantPoolBase.sol::54 => require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");

contracts/liquid-staking/GiantPoolBase.sol::55 => require(idleETH >= _amount, "Come back later or withdraw less ETH");

contracts/liquid-staking/GiantPoolBase.sol::61 => require(success, "Failed to transfer ETH");

contracts/liquid-staking/GiantPoolBase.sol::71 => require(amountOfTokens > 0, "Empty arrays");

contracts/liquid-staking/GiantPoolBase.sol::72 => require(amountOfTokens == _amounts.length, "Inconsistent array lengths");

contracts/liquid-staking/GiantPoolBase.sol::94 => require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

contracts/liquid-staking/GiantPoolBase.sol::95 => require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");

contracts/liquid-staking/GiantPoolBase.sol::96 => require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");

contracts/liquid-staking/LSDNFactory.sol::51 => require(_liquidStakingManagerImplementation != address(0), "Zero Address");

contracts/liquid-staking/LSDNFactory.sol::52 => require(_syndicateFactory != address(0), "Zero Address");

contracts/liquid-staking/LSDNFactory.sol::53 => require(_lpTokenFactory != address(0), "Zero Address");

contracts/liquid-staking/LSDNFactory.sol::54 => require(_smartWalletFactory != address(0), "Zero Address");

contracts/liquid-staking/LSDNFactory.sol::55 => require(_brand != address(0), "Zero Address");

contracts/liquid-staking/LSDNFactory.sol::56 => require(_savETHVaultDeployer != address(0), "Zero Address");

contracts/liquid-staking/LSDNFactory.sol::57 => require(_stakingFundsVaultDeployer != address(0), "Zero Address");

contracts/liquid-staking/LSDNFactory.sol::58 => require(_optionalGatekeeperDeployer != address(0), "Zero Address");

contracts/liquid-staking/GiantSavETHVaultPool.sol::36 => require(numOfSavETHVaults > 0, "Empty arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::37 => require(numOfSavETHVaults == _ETHTransactionAmounts.length, "Inconsistent array lengths");

contracts/liquid-staking/GiantSavETHVaultPool.sol::38 => require(numOfSavETHVaults == _blsPublicKeys.length, "Inconsistent array lengths");

contracts/liquid-staking/GiantSavETHVaultPool.sol::39 => require(numOfSavETHVaults == _stakeAmounts.length, "Inconsistent array lengths");

contracts/liquid-staking/GiantSavETHVaultPool.sol::72 => require(numOfVaults > 0, "Empty arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::73 => require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::74 => require(numOfVaults == _amounts.length, "Inconsistent arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::89 => require(vault.isDETHReadyForWithdrawal(address(token)), "dETH is not ready for withdrawal");

contracts/liquid-staking/GiantSavETHVaultPool.sol::92 => require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");

contracts/liquid-staking/GiantSavETHVaultPool.sol::123 => require(numOfRotations > 0, "Empty arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::124 => require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::125 => require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::126 => require(numOfRotations == _amounts.length, "Inconsistent arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::127 => require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

contracts/liquid-staking/GiantSavETHVaultPool.sol::143 => require(numOfVaults > 0, "Empty arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::144 => require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

contracts/liquid-staking/GiantSavETHVaultPool.sol::145 => require(numOfVaults == _amounts.length, "Inconsistent arrays");

contracts/smart-wallet/OwnableSmartWallet.sol::79 => require(result, "Failed to execute");

contracts/liquid-staking/SavETHVault.sol::50 => require(msg.sender == address(liquidStakingManager), "Not the savETH vault manager");

contracts/liquid-staking/SavETHVault.sol::59 => require(numOfValidators > 0, "Empty arrays");

contracts/liquid-staking/SavETHVault.sol::60 => require(numOfValidators == _amounts.length, "Inconsistent array lengths");

contracts/liquid-staking/SavETHVault.sol::64 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

contracts/liquid-staking/SavETHVault.sol::76 => require(msg.value == totalAmount, "Invalid ETH amount attached");

contracts/liquid-staking/SavETHVault.sol::84 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/SavETHVault.sol::90 => require(msg.value == _amount, "Must provide correct amount of ETH");

contracts/liquid-staking/SavETHVault.sol::101 => require(numOfTokens > 0, "Empty arrays");

contracts/liquid-staking/SavETHVault.sol::102 => require(numOfTokens == _amounts.length, "Inconsistent array length");

contracts/liquid-staking/SavETHVault.sol::114 => require(numOfTokens > 0, "Empty arrays");

contracts/liquid-staking/SavETHVault.sol::115 => require(numOfTokens == _amounts.length, "Inconsisent array length");

contracts/liquid-staking/SavETHVault.sol::127 => require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

contracts/liquid-staking/SavETHVault.sol::128 => require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

contracts/liquid-staking/SavETHVault.sol::161 => require(dETHBalance >= 24 ether, "Nothing to withdraw");

contracts/liquid-staking/SavETHVault.sol::186 => require(isStaleLiquidity, "Liquidity is still fresh");

contracts/liquid-staking/SavETHVault.sol::190 => require(result, "Transfer failed");

contracts/liquid-staking/SavETHVault.sol::204 => require(_amount >= 24 ether, "Amount cannot be less than 24 ether");

contracts/liquid-staking/SavETHVault.sol::205 => require(address(this).balance >= _amount, "Insufficient withdrawal amount");

contracts/liquid-staking/SavETHVault.sol::206 => require(_smartWallet != address(0), "Zero address");

contracts/liquid-staking/SavETHVault.sol::207 => require(_smartWallet != address(this), "This address");

contracts/liquid-staking/SavETHVault.sol::210 => require(result, "Transfer failed");

contracts/liquid-staking/SavETHVault.sol::236 => require(_liquidStakingManagerAddress != address(0), "Zero address");

contracts/liquid-staking/SavETHVault.sol::237 => require(address(_lpTokenFactory) != address(0), "Zero address");

contracts/liquid-staking/GiantMevAndFeesPool.sol::35 => require(numOfVaults > 0, "Zero vaults");

contracts/liquid-staking/GiantMevAndFeesPool.sol::36 => require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");

contracts/liquid-staking/GiantMevAndFeesPool.sol::37 => require(numOfVaults == _amounts.length, "Inconsistent lengths");

contracts/liquid-staking/GiantMevAndFeesPool.sol::62 => require(numOfVaults > 0, "Empty array");

contracts/liquid-staking/GiantMevAndFeesPool.sol::63 => require(numOfVaults == _blsPublicKeysForKnots.length, "Inconsistent array lengths");

contracts/liquid-staking/GiantMevAndFeesPool.sol::87 => require(_stakingFundsVaults.length == _lpTokens.length, "Inconsistent array lengths");

contracts/liquid-staking/GiantMevAndFeesPool.sol::112 => require(numOfRotations > 0, "Empty arrays");

contracts/liquid-staking/GiantMevAndFeesPool.sol::113 => require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");

contracts/liquid-staking/GiantMevAndFeesPool.sol::114 => require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

contracts/liquid-staking/GiantMevAndFeesPool.sol::115 => require(numOfRotations == _amounts.length, "Inconsistent arrays");

contracts/liquid-staking/GiantMevAndFeesPool.sol::116 => require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

contracts/liquid-staking/GiantMevAndFeesPool.sol::132 => require(numOfVaults > 0, "Empty arrays");

contracts/liquid-staking/GiantMevAndFeesPool.sol::133 => require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

contracts/liquid-staking/GiantMevAndFeesPool.sol::134 => require(numOfVaults == _amounts.length, "Inconsistent arrays");

contracts/liquid-staking/GiantMevAndFeesPool.sol::147 => require(msg.sender == address(lpTokenETH), "Caller is not giant LP");

contracts/liquid-staking/GiantMevAndFeesPool.sol::171 => require(msg.sender == address(lpTokenETH), "Caller is not giant LP");

contracts/liquid-staking/StakingFundsVault.sol::51 => require(msg.sender == address(liquidStakingNetworkManager), "Only network manager");

contracts/liquid-staking/StakingFundsVault.sol::71 => require(numOfValidators > 0, "Empty arrays");

contracts/liquid-staking/StakingFundsVault.sol::72 => require(numOfValidators == _amounts.length, "Inconsistent array lengths");

contracts/liquid-staking/StakingFundsVault.sol::79 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

contracts/liquid-staking/StakingFundsVault.sol::107 => require(msg.value == totalAmount, "Invalid ETH amount attached");

contracts/liquid-staking/StakingFundsVault.sol::114 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/StakingFundsVault.sol::120 => require(msg.value == _amount, "Must provide correct amount of ETH");

contracts/liquid-staking/StakingFundsVault.sol::150 => require(numOfTokens > 0, "Empty arrays");

contracts/liquid-staking/StakingFundsVault.sol::151 => require(numOfTokens == _amounts.length, "Inconsistent array length");

contracts/liquid-staking/StakingFundsVault.sol::154 => require(address(token) != address(0), "No ETH staked for specified BLS key");

contracts/liquid-staking/StakingFundsVault.sol::164 => require(numOfTokens > 0, "Empty arrays");

contracts/liquid-staking/StakingFundsVault.sol::165 => require(numOfTokens == _amounts.length, "Inconsistent array length");

contracts/liquid-staking/StakingFundsVault.sol::175 => require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

contracts/liquid-staking/StakingFundsVault.sol::176 => require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

contracts/liquid-staking/StakingFundsVault.sol::177 => require(address(_lpToken) != address(0), "Zero address specified");

contracts/liquid-staking/StakingFundsVault.sol::184 => require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");

contracts/liquid-staking/StakingFundsVault.sol::191 => require(result, "Transfer failed");

contracts/liquid-staking/StakingFundsVault.sol::229 => require(address(token) != address(0), "Invalid BLS key");

contracts/liquid-staking/StakingFundsVault.sol::230 => require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");

contracts/liquid-staking/StakingFundsVault.sol::240 => require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

contracts/liquid-staking/StakingFundsVault.sol::241 => require(_amount <= address(this).balance, "Not enough ETH to withdraw");

contracts/liquid-staking/StakingFundsVault.sol::242 => require(_wallet != address(0), "Zero address");

contracts/liquid-staking/StakingFundsVault.sol::245 => require(result, "Transfer failed");

contracts/liquid-staking/StakingFundsVault.sol::267 => require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");

contracts/liquid-staking/StakingFundsVault.sol::320 => require(blsPubKey.length > 0, "Invalid token");

contracts/liquid-staking/StakingFundsVault.sol::346 => require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");

contracts/liquid-staking/StakingFundsVault.sol::361 => require(_syndicate != address(0), "Invalid configuration");

contracts/liquid-staking/StakingFundsVault.sol::372 => require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");

contracts/liquid-staking/StakingFundsVault.sol::373 => require(address(_lpTokenFactory) != address(0), "Zero Address");

contracts/liquid-staking/LiquidStakingManager.sol::161 => require(msg.sender == dao, "Must be DAO");

contracts/liquid-staking/LiquidStakingManager.sol::209 => require(smartWallet != address(0), "No wallet found");

contracts/liquid-staking/LiquidStakingManager.sol::240 => require(_newAddress != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::241 => require(_newAddress != dao, "Same address");

contracts/liquid-staking/LiquidStakingManager.sol::250 => require(_commissionPercentage != daoCommissionPercentage, "Same commission percentage");

contracts/liquid-staking/LiquidStakingManager.sol::256 => require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

contracts/liquid-staking/LiquidStakingManager.sol::257 => require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

contracts/liquid-staking/LiquidStakingManager.sol::258 => require(numberOfKnots == 0, "Cannot change ticker once house is created");

contracts/liquid-staking/LiquidStakingManager.sol::268 => require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");

contracts/liquid-staking/LiquidStakingManager.sol::279 => require(_nodeRunner != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::280 => require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

contracts/liquid-staking/LiquidStakingManager.sol::290 => require(_newRepresentative != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::291 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::294 => require(smartWallet != address(0), "No smart wallet");

contracts/liquid-staking/LiquidStakingManager.sol::295 => require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");

contracts/liquid-staking/LiquidStakingManager.sol::296 => require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

contracts/liquid-staking/LiquidStakingManager.sol::309 => require(_newRepresentative != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::312 => require(smartWallet != address(0), "No smart wallet");

contracts/liquid-staking/LiquidStakingManager.sol::313 => require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");

contracts/liquid-staking/LiquidStakingManager.sol::314 => require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

contracts/liquid-staking/LiquidStakingManager.sol::327 => require(_recipient != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::328 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::331 => require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");

contracts/liquid-staking/LiquidStakingManager.sol::332 => require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::333 => require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");

contracts/liquid-staking/LiquidStakingManager.sol::357 => require(_new != address(0) && _current != _new, "New is zero or current");

contracts/liquid-staking/LiquidStakingManager.sol::360 => require(wallet != address(0), "Wallet does not exist");

contracts/liquid-staking/LiquidStakingManager.sol::361 => require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");

contracts/liquid-staking/LiquidStakingManager.sol::364 => require(newRunnerCurrentWallet == address(0), "New runner has a wallet");

contracts/liquid-staking/LiquidStakingManager.sol::386 => require(_blsPubKeys.length > 0, "No BLS keys specified");

contracts/liquid-staking/LiquidStakingManager.sol::387 => require(_recipient != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::390 => require(smartWallet != address(0), "Unknown node runner");

contracts/liquid-staking/LiquidStakingManager.sol::393 => require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::396 => require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

contracts/liquid-staking/LiquidStakingManager.sol::412 => require(transferResult, "Failed to transfer");

contracts/liquid-staking/LiquidStakingManager.sol::416 => require(transferResult, "Failed to transfer");

contracts/liquid-staking/LiquidStakingManager.sol::432 => require(len >= 1, "No value provided");

contracts/liquid-staking/LiquidStakingManager.sol::433 => require(len == _blsSignatures.length, "Unequal number of array values");

contracts/liquid-staking/LiquidStakingManager.sol::434 => require(msg.value == len * 4 ether, "Insufficient ether provided");

contracts/liquid-staking/LiquidStakingManager.sol::435 => require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");

contracts/liquid-staking/LiquidStakingManager.sol::436 => require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

contracts/liquid-staking/LiquidStakingManager.sol::437 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::455 => require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");

contracts/liquid-staking/LiquidStakingManager.sol::461 => require(result, "Transfer failed");

contracts/liquid-staking/LiquidStakingManager.sol::469 => require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::532 => require(numOfValidators > 0, "No data");

contracts/liquid-staking/LiquidStakingManager.sol::533 => require(numOfValidators == _ciphertexts.length, "Inconsistent array lengths");

contracts/liquid-staking/LiquidStakingManager.sol::534 => require(numOfValidators == _aesEncryptorKeys.length, "Inconsistent array lengths");

contracts/liquid-staking/LiquidStakingManager.sol::535 => require(numOfValidators == _encryptionSignatures.length, "Inconsistent array lengths");

contracts/liquid-staking/LiquidStakingManager.sol::536 => require(numOfValidators == _dataRoots.length, "Inconsistent array lengths");

contracts/liquid-staking/LiquidStakingManager.sol::541 => require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::544 => require(associatedSmartWallet != address(0), "Unknown BLS public key");

contracts/liquid-staking/LiquidStakingManager.sol::583 => require(numOfKnotsToProcess > 0, "Empty array");

contracts/liquid-staking/LiquidStakingManager.sol::584 => require(numOfKnotsToProcess == _beaconChainBalanceReports.length, "Inconsistent array lengths");

contracts/liquid-staking/LiquidStakingManager.sol::585 => require(numOfKnotsToProcess == _reportSignatures.length, "Inconsistent array lengths");

contracts/liquid-staking/LiquidStakingManager.sol::589 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

contracts/liquid-staking/LiquidStakingManager.sol::658 => require(_dao != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::659 => require(_syndicateFactory != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::660 => require(_smartWalletFactory != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::661 => require(_brand != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::662 => require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");

contracts/liquid-staking/LiquidStakingManager.sol::663 => require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

contracts/liquid-staking/LiquidStakingManager.sol::685 => require(_nodeRunner != address(0), "Zero address");

contracts/liquid-staking/LiquidStakingManager.sol::688 => require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

contracts/liquid-staking/LiquidStakingManager.sol::734 => revert("Unexpected state");

contracts/liquid-staking/LiquidStakingManager.sol::922 => require(_received > 0, "Nothing received");

contracts/liquid-staking/LiquidStakingManager.sol::936 => require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

contracts/liquid-staking/LiquidStakingManager.sol::939 => require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");

contracts/liquid-staking/LiquidStakingManager.sol::940 => require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

contracts/liquid-staking/LiquidStakingManager.sol::943 => require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");

contracts/liquid-staking/LiquidStakingManager.sol::944 => require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");

contracts/liquid-staking/LiquidStakingManager.sol::949 => require(_commissionPercentage <= MODULO, "Invalid commission");

contracts/liquid-staking/SyndicateRewardsProcessor.sol::57 => require(_recipient != address(0), "Zero address");

contracts/liquid-staking/SyndicateRewardsProcessor.sol::68 => require(success, "Failed to transfer");

contracts/liquid-staking/ETHPoolLPFactory.sol::59 => require(numOfRotations > 0, "Empty arrays");

contracts/liquid-staking/ETHPoolLPFactory.sol::60 => require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

contracts/liquid-staking/ETHPoolLPFactory.sol::61 => require(numOfRotations == _amounts.length, "Inconsistent arrays");

contracts/liquid-staking/ETHPoolLPFactory.sol::77 => require(address(_oldLPToken) != address(0), "Zero address");

contracts/liquid-staking/ETHPoolLPFactory.sol::78 => require(address(_newLPToken) != address(0), "Zero address");

contracts/liquid-staking/ETHPoolLPFactory.sol::79 => require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");

contracts/liquid-staking/ETHPoolLPFactory.sol::80 => require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

contracts/liquid-staking/ETHPoolLPFactory.sol::81 => require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");

contracts/liquid-staking/ETHPoolLPFactory.sol::82 => require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");

contracts/liquid-staking/ETHPoolLPFactory.sol::83 => require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

contracts/liquid-staking/ETHPoolLPFactory.sol::88 => require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");

contracts/liquid-staking/ETHPoolLPFactory.sol::89 => require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");

contracts/liquid-staking/ETHPoolLPFactory.sol::111 => require(_amount >= MIN_STAKING_AMOUNT, "Min amount not reached");

contracts/liquid-staking/ETHPoolLPFactory.sol::112 => require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");

contracts/liquid-staking/ETHPoolLPFactory.sol::122 => require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

contracts/liquid-staking/ETHPoolLPFactory.sol::130 => require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

## 13. Use shift right/left instead of division/multiplication if possible (2 instances)

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left. While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```solidity
contracts/syndicate/Syndicate.sol::378 => return ethPerKnot / 2;

contracts/liquid-staking/LiquidStakingManager.sol::870 => sETH.approve(syndicate, (2 ** 256) - 1);
```

## 14. Functions guaranteed to revert when called by normal users can be marked `payable` (15 instances)

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```solidity
contracts/liquid-staking/LPToken.sol::46 => function mint(address _recipient, uint256 _amount) external onlyDeployer {

contracts/liquid-staking/LPToken.sol::51 => function burn(address _recipient, uint256 _amount) external onlyDeployer {

contracts/smart-wallet/OwnableSmartWallet.sol::114 => function setApproval(address to, bool status) external onlyOwner override {

contracts/liquid-staking/StakingFundsVault.sol::56 => function updateDerivativesMinted() external onlyManager {

contracts/liquid-staking/StakingFundsVault.sol::239 => function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {

contracts/syndicate/Syndicate.sol::154 => function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {

contracts/syndicate/Syndicate.sol::161 => function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {

contracts/syndicate/Syndicate.sol::168 => function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {

contracts/liquid-staking/LiquidStakingManager.sol::218 => function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {

contracts/liquid-staking/LiquidStakingManager.sol::239 => function updateDAOAddress(address _newAddress) external onlyDAO {

contracts/liquid-staking/LiquidStakingManager.sol::249 => function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {

contracts/liquid-staking/LiquidStakingManager.sol::255 => function updateTicker(string calldata _newTicker) external onlyDAO {

contracts/liquid-staking/LiquidStakingManager.sol::267 => function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {

contracts/liquid-staking/LiquidStakingManager.sol::278 => function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {

contracts/liquid-staking/LiquidStakingManager.sol::308 => function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO {
```

## Use bitshift to calculate power of `2` with `1 << x` instead of `2**x`
```solidity
contracts/liquid-staking/LiquidStakingManager.sol:870:        sETH.approve(syndicate, (2 ** 256) - 1);
```

## Cache `.length` in loops
**Summary:**  Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.
```solidity
contracts/syndicate/Syndicate.sol:211:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:259:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:301:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:346:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:585:        for (uint256 i; i < _priorityStakers.length; ++i) {
contracts/syndicate/Syndicate.sol:598:        for (uint256 i; i < _blsPublicKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:648:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/liquid-staking/GiantMevAndFeesPool.sol:90:        for (uint256 i; i < _stakingFundsVaults.length; ++i) {
contracts/liquid-staking/GiantMevAndFeesPool.sol:183:        for (uint256 i; i < _lpTokens.length; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:203:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:266:        for (uint256 i; i < _blsPublicKeys.length; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:276:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:286:        for (uint256 i; i < _token.length; ++i) {
contracts/liquid-staking/LiquidStakingManager.sol:392:        for(uint256 i; i < _blsPubKeys.length; ++i) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:82:            for (uint256 j; j < _lpTokens[i].length; ++j) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:148:            for (uint256 j; j < _lpTokens[i].length; ++j) {
```

##  Multiple address mappings can be combined into a single mapping of an `address` to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.
```solidity
contracts/smart-wallet/OwnableSmartWallet.sol:22:    mapping(address => mapping(address => bool)) internal _isTransferApproved;
contracts/liquid-staking/SyndicateRewardsProcessor.sol:27:    mapping(address => mapping(address => uint256)) public claimed;
```
## Use `!= 0` instead of `> 0`
```solidity
contracts/syndicate/Syndicate.sol:177:        if (numberOfRegisteredKnots > 0) {
contracts/syndicate/Syndicate.sol:183:            if (totalFreeFloatingShares > 0) {
contracts/syndicate/Syndicate.sol:315:            if (unclaimedUserShare > 0) {
contracts/syndicate/Syndicate.sol:500:        if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {
contracts/syndicate/Syndicate.sol:551:        return totalFreeFloatingShares > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingShares : 0;
contracts/syndicate/Syndicate.sol:588:            if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();
contracts/syndicate/Syndicate.sol:634:        lastAccumulatedETHPerFreeFloatingShare[_blsPublicKey] > 0 ?
contracts/syndicate/Syndicate.sol:656:            if (unclaimedUserShare > 0) {
contracts/liquid-staking/GiantPoolBase.sol:71:        require(amountOfTokens > 0, "Empty arrays");
contracts/liquid-staking/SavETHVault.sol:59:        require(numOfValidators > 0, "Empty arrays");
contracts/liquid-staking/SavETHVault.sol:101:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/SavETHVault.sol:114:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:35:        require(numOfVaults > 0, "Zero vaults");
contracts/liquid-staking/GiantMevAndFeesPool.sol:62:        require(numOfVaults > 0, "Empty array");
contracts/liquid-staking/GiantMevAndFeesPool.sol:112:        require(numOfRotations > 0, "Empty arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:132:        require(numOfVaults > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:71:        require(numOfValidators > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:150:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:164:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:320:            require(blsPubKey.length > 0, "Invalid token");
contracts/liquid-staking/StakingFundsVault.sol:346:            require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");
contracts/liquid-staking/LiquidStakingManager.sol:386:        require(_blsPubKeys.length > 0, "No BLS keys specified");
contracts/liquid-staking/LiquidStakingManager.sol:414:        if (daoAmount > 0) {
contracts/liquid-staking/LiquidStakingManager.sol:532:        require(numOfValidators > 0, "No data");
contracts/liquid-staking/LiquidStakingManager.sol:583:        require(numOfKnotsToProcess > 0, "Empty array");
contracts/liquid-staking/LiquidStakingManager.sol:922:        require(_received > 0, "Nothing received");
contracts/liquid-staking/LiquidStakingManager.sol:924:        if (daoCommissionPercentage > 0) {
contracts/liquid-staking/ETHPoolLPFactory.sol:59:        require(numOfRotations > 0, "Empty arrays");
contracts/liquid-staking/SyndicateRewardsProcessor.sol:37:        if (_balanceOfSender > 0) {
contracts/liquid-staking/SyndicateRewardsProcessor.sol:59:        if (balance > 0) {
contracts/liquid-staking/SyndicateRewardsProcessor.sol:62:            if (due > 0) {
contracts/liquid-staking/SyndicateRewardsProcessor.sol:77:        if (_numOfShares > 0) {
contracts/liquid-staking/SyndicateRewardsProcessor.sol:81:            if (unprocessed > 0) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:36:        require(numOfSavETHVaults > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:72:        require(numOfVaults > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:123:        require(numOfRotations > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:143:        require(numOfVaults > 0, "Empty arrays");
```
## Use `abi.encodePacked` for gas optimization 
Changing the abi.encode function to abi.encodePacked  can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient.
```solidity
contracts/syndicate/SyndicateFactory.sol:63:        return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));
```
## `<x> += <y>` costs more gas than `<x> = <x> + <y>` 
```solidity
contracts/syndicate/Syndicate.sol:185:                accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);
contracts/syndicate/Syndicate.sol:190:            accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
contracts/syndicate/Syndicate.sol:225:            totalFreeFloatingShares += _sETHAmount;
contracts/syndicate/Syndicate.sol:226:            sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
contracts/syndicate/Syndicate.sol:227:            sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
contracts/syndicate/Syndicate.sol:269:                totalFreeFloatingShares -= _sETHAmount;
contracts/syndicate/Syndicate.sol:272:            sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
contracts/syndicate/Syndicate.sol:273:            sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;
contracts/syndicate/Syndicate.sol:317:                totalClaimed += unclaimedUserShare;
contracts/syndicate/Syndicate.sol:430:                    currentAccrued +=
contracts/syndicate/Syndicate.sol:511:                    accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;
contracts/syndicate/Syndicate.sol:521:                        accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
contracts/syndicate/Syndicate.sol:558:        numberOfRegisteredKnots += knotsToRegister;
contracts/syndicate/Syndicate.sol:621:        totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];
contracts/syndicate/Syndicate.sol:624:        numberOfRegisteredKnots -= 1;
contracts/syndicate/Syndicate.sol:658:                totalClaimed += unclaimedUserShare;
contracts/liquid-staking/GiantPoolBase.sol:39:        idleETH += msg.value;
contracts/liquid-staking/GiantPoolBase.sol:57:        idleETH -= _amount;
contracts/liquid-staking/SavETHVault.sol:71:            totalAmount += amount;
contracts/liquid-staking/GiantMevAndFeesPool.sol:40:            idleETH -= _ETHTransactionAmounts[i];
contracts/liquid-staking/StakingFundsVault.sol:58:        totalShares += 4 ether;
contracts/liquid-staking/StakingFundsVault.sol:97:            totalAmount += amount;
contracts/liquid-staking/StakingFundsVault.sol:278:            totalAccumulated += previewAccumulatedETH(_user, token);
contracts/liquid-staking/StakingFundsVault.sol:287:            totalAccumulated += previewAccumulatedETH(_user, _token[i]);
contracts/liquid-staking/LiquidStakingManager.sol:615:            stakedKnotsOfSmartWallet[smartWallet] -= 1;
contracts/liquid-staking/LiquidStakingManager.sol:770:        stakedKnotsOfSmartWallet[smartWallet] += 1;
contracts/liquid-staking/LiquidStakingManager.sol:782:        numberOfKnots += 1;
contracts/liquid-staking/LiquidStakingManager.sol:839:        numberOfKnots += 1;
contracts/liquid-staking/SyndicateRewardsProcessor.sol:65:                totalClaimed += due;
contracts/liquid-staking/SyndicateRewardsProcessor.sol:85:                accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;
contracts/liquid-staking/GiantSavETHVaultPool.sol:46:            idleETH -= transactionAmount;
```


## Use `private` for `constants`
```solidity
contracts/syndicate/Syndicate.sol:66:    uint256 public constant PRECISION = 1e24;
contracts/liquid-staking/GiantPoolBase.sol:22:    uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
contracts/liquid-staking/ETHPoolLPFactory.sol:38:    uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
contracts/liquid-staking/SyndicateRewardsProcessor.sol:15:    uint256 public constant PRECISION = 1e24;
```
##  Use `calldata` instead of `memory` when used only for reading
```solidity
contracts/smart-wallet/OwnableSmartWallet.sol:41:    function execute(address target, bytes memory callData)
contracts/syndicate/Syndicate.sol:337:    function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {
contracts/syndicate/Syndicate.sol:343:    function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {
contracts/syndicate/Syndicate.sol:359:    function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {
contracts/syndicate/Syndicate.sol:491:    function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {
contracts/syndicate/Syndicate.sol:555:    function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots) internal {
contracts/syndicate/Syndicate.sol:583:    function _addPriorityStakers(address[] memory _priorityStakers) internal {
contracts/syndicate/Syndicate.sol:610:    function _deRegisterKnot(bytes memory _blsPublicKey) internal {
contracts/liquid-staking/StakingFundsVault.sol:355:    function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {
contracts/liquid-staking/StakingFundsVault.sol:360:    function _claimFundsFromSyndicateForDistribution(address _syndicate, bytes[] memory _blsPubKeys) internal {
contracts/liquid-staking/LiquidStakingManager.sol:879:    function _autoStakeWithSyndicate(address _associatedSmartWallet, bytes memory _blsPubKey) internal {
```

## Use `variable == false|0` -> `!variable` or `variable ==  true` -> `variable`
```solidity
contracts/syndicate/Syndicate.sol:204:        if (_blsPubKeys.length == 0) revert EmptyArray();
contracts/syndicate/Syndicate.sol:251:        if (_blsPubKeys.length == 0) revert EmptyArray();
contracts/syndicate/Syndicate.sol:294:        if (_blsPubKeys.length == 0) revert EmptyArray();
contracts/syndicate/Syndicate.sol:345:        if (numOfKeys == 0) revert EmptyArray();
contracts/syndicate/Syndicate.sol:557:        if (knotsToRegister == 0) revert EmptyArray();
contracts/syndicate/Syndicate.sol:584:        if (_priorityStakers.length == 0) revert EmptyArray();
contracts/syndicate/Syndicate.sol:611:        if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();
contracts/syndicate/Syndicate.sol:612:        if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
contracts/syndicate/Syndicate.sol:641:        if (_blsPubKeys.length == 0) revert EmptyArray();
contracts/liquid-staking/SavETHVault.sol:64:            require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
contracts/liquid-staking/SavETHVault.sol:84:        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/StakingFundsVault.sol:79:            require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
contracts/liquid-staking/StakingFundsVault.sol:114:        require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/StakingFundsVault.sol:205:                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
contracts/liquid-staking/StakingFundsVault.sol:215:            if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {
contracts/liquid-staking/LiquidStakingManager.sol:258:        require(numberOfKnots == 0, "Cannot change ticker once house is created");
contracts/liquid-staking/LiquidStakingManager.sol:291:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:295:        require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
contracts/liquid-staking/LiquidStakingManager.sol:313:        require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
contracts/liquid-staking/LiquidStakingManager.sol:328:        require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:332:        require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:393:            require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:436:        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
contracts/liquid-staking/LiquidStakingManager.sol:437:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:469:            require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:541:            require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:589:            require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:598:            if(numberOfKnots == 0) {
contracts/liquid-staking/LiquidStakingManager.sol:617:            if(stakedKnotsOfSmartWallet[smartWallet] == 0) {
contracts/liquid-staking/LiquidStakingManager.sol:688:            require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
contracts/liquid-staking/GiantSavETHVaultPool.sol:150:                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
```

## [G‑06]` >=` COSTS LESS GAS THAN `>`
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde 
```solidity
contracts/syndicate/Syndicate.sol:177:        if (numberOfRegisteredKnots > 0) {
contracts/syndicate/Syndicate.sol:183:            if (totalFreeFloatingShares > 0) {
contracts/syndicate/Syndicate.sol:223:            if (_sETHAmount + totalStaked > 12 ether) revert InvalidStakeAmount();
contracts/syndicate/Syndicate.sol:315:            if (unclaimedUserShare > 0) {
contracts/syndicate/Syndicate.sol:482:        if (_priorityStakingEndBlock > block.number) {
contracts/syndicate/Syndicate.sol:500:        if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {
contracts/syndicate/Syndicate.sol:551:        return totalFreeFloatingShares > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingShares : 0;
contracts/syndicate/Syndicate.sol:588:            if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();
contracts/syndicate/Syndicate.sol:634:        lastAccumulatedETHPerFreeFloatingShare[_blsPublicKey] > 0 ?
contracts/syndicate/Syndicate.sol:656:            if (unclaimedUserShare > 0) {
contracts/liquid-staking/GiantPoolBase.sol:71:        require(amountOfTokens > 0, "Empty arrays");
contracts/liquid-staking/SavETHVault.sol:59:        require(numOfValidators > 0, "Empty arrays");
contracts/liquid-staking/SavETHVault.sol:101:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/SavETHVault.sol:114:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:35:        require(numOfVaults > 0, "Zero vaults");
contracts/liquid-staking/GiantMevAndFeesPool.sol:62:        require(numOfVaults > 0, "Empty array");
contracts/liquid-staking/GiantMevAndFeesPool.sol:112:        require(numOfRotations > 0, "Empty arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:132:        require(numOfVaults > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:71:        require(numOfValidators > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:150:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:164:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:320:            require(blsPubKey.length > 0, "Invalid token");
contracts/liquid-staking/StakingFundsVault.sol:346:            require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");
contracts/liquid-staking/LiquidStakingManager.sol:386:        require(_blsPubKeys.length > 0, "No BLS keys specified");
contracts/liquid-staking/LiquidStakingManager.sol:414:        if (daoAmount > 0) {
contracts/liquid-staking/LiquidStakingManager.sol:532:        require(numOfValidators > 0, "No data");
contracts/liquid-staking/LiquidStakingManager.sol:583:        require(numOfKnotsToProcess > 0, "Empty array");
contracts/liquid-staking/LiquidStakingManager.sol:922:        require(_received > 0, "Nothing received");
contracts/liquid-staking/LiquidStakingManager.sol:924:        if (daoCommissionPercentage > 0) {
contracts/liquid-staking/ETHPoolLPFactory.sol:59:        require(numOfRotations > 0, "Empty arrays");
contracts/liquid-staking/SyndicateRewardsProcessor.sol:37:        if (_balanceOfSender > 0) {
contracts/liquid-staking/SyndicateRewardsProcessor.sol:59:        if (balance > 0) {
contracts/liquid-staking/SyndicateRewardsProcessor.sol:62:            if (due > 0) {
contracts/liquid-staking/SyndicateRewardsProcessor.sol:77:        if (_numOfShares > 0) {
contracts/liquid-staking/SyndicateRewardsProcessor.sol:81:            if (unprocessed > 0) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:36:        require(numOfSavETHVaults > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:72:        require(numOfVaults > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:123:        require(numOfRotations > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:143:        require(numOfVaults > 0, "Empty arrays");
```





## Use custom errors to save deployment and runtime costs in case of revert
Instead of using strings for error messages (e.g., `require(msg.sender == owner, “unauthorized”)`), you can use custom errors to reduce both deployment and runtime gas costs. In addition, they are very convenient as you can easily pass dynamic information to them.
```solidity
contracts/smart-wallet/OwnableSmartWallet.sol:33:        require(
contracts/smart-wallet/OwnableSmartWallet.sol:79:        require(result, "Failed to execute");
contracts/smart-wallet/OwnableSmartWallet.sol:100:        require(
contracts/smart-wallet/OwnableSmartWallet.sol:115:        require(
contracts/smart-wallet/OwnableSmartWalletFactory.sol:37:        require(owner != address(0), 'Wallet cannot be address 0');
contracts/liquid-staking/GiantPoolBase.sol:35:        require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");
contracts/liquid-staking/GiantPoolBase.sol:36:        require(msg.value == _amount, "Value equal to amount");
contracts/liquid-staking/GiantPoolBase.sol:53:        require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
contracts/liquid-staking/GiantPoolBase.sol:54:        require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");
contracts/liquid-staking/GiantPoolBase.sol:55:        require(idleETH >= _amount, "Come back later or withdraw less ETH");
contracts/liquid-staking/GiantPoolBase.sol:61:        require(success, "Failed to transfer ETH");
contracts/liquid-staking/GiantPoolBase.sol:71:        require(amountOfTokens > 0, "Empty arrays");
contracts/liquid-staking/GiantPoolBase.sol:72:        require(amountOfTokens == _amounts.length, "Inconsistent array lengths");
contracts/liquid-staking/GiantPoolBase.sol:94:        require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
contracts/liquid-staking/GiantPoolBase.sol:95:        require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");
contracts/liquid-staking/GiantPoolBase.sol:96:        require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");
contracts/liquid-staking/SavETHVault.sol:50:        require(msg.sender == address(liquidStakingManager), "Not the savETH vault manager");
contracts/liquid-staking/SavETHVault.sol:59:        require(numOfValidators > 0, "Empty arrays");
contracts/liquid-staking/SavETHVault.sol:60:        require(numOfValidators == _amounts.length, "Inconsistent array lengths");
contracts/liquid-staking/SavETHVault.sol:64:            require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
contracts/liquid-staking/SavETHVault.sol:65:            require(
contracts/liquid-staking/SavETHVault.sol:76:        require(msg.value == totalAmount, "Invalid ETH amount attached");
contracts/liquid-staking/SavETHVault.sol:84:        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/SavETHVault.sol:85:        require(
contracts/liquid-staking/SavETHVault.sol:90:        require(msg.value == _amount, "Must provide correct amount of ETH");
contracts/liquid-staking/SavETHVault.sol:101:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/SavETHVault.sol:102:        require(numOfTokens == _amounts.length, "Inconsistent array length");
contracts/liquid-staking/SavETHVault.sol:114:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/SavETHVault.sol:115:        require(numOfTokens == _amounts.length, "Inconsisent array length");
contracts/liquid-staking/SavETHVault.sol:127:        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
contracts/liquid-staking/SavETHVault.sol:128:        require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");
contracts/liquid-staking/SavETHVault.sol:134:        require(
contracts/liquid-staking/SavETHVault.sol:161:                require(dETHBalance >= 24 ether, "Nothing to withdraw");
contracts/liquid-staking/SavETHVault.sol:186:        require(isStaleLiquidity, "Liquidity is still fresh");
contracts/liquid-staking/SavETHVault.sol:190:        require(result, "Transfer failed");
contracts/liquid-staking/SavETHVault.sol:204:        require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
contracts/liquid-staking/SavETHVault.sol:205:        require(address(this).balance >= _amount, "Insufficient withdrawal amount");
contracts/liquid-staking/SavETHVault.sol:206:        require(_smartWallet != address(0), "Zero address");
contracts/liquid-staking/SavETHVault.sol:207:        require(_smartWallet != address(this), "This address");
contracts/liquid-staking/SavETHVault.sol:210:        require(result, "Transfer failed");
contracts/liquid-staking/SavETHVault.sol:236:        require(_liquidStakingManagerAddress != address(0), "Zero address");
contracts/liquid-staking/SavETHVault.sol:237:        require(address(_lpTokenFactory) != address(0), "Zero address");
contracts/liquid-staking/GiantMevAndFeesPool.sol:35:        require(numOfVaults > 0, "Zero vaults");
contracts/liquid-staking/GiantMevAndFeesPool.sol:36:        require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");
contracts/liquid-staking/GiantMevAndFeesPool.sol:37:        require(numOfVaults == _amounts.length, "Inconsistent lengths");
contracts/liquid-staking/GiantMevAndFeesPool.sol:43:            require(
contracts/liquid-staking/GiantMevAndFeesPool.sol:62:        require(numOfVaults > 0, "Empty array");
contracts/liquid-staking/GiantMevAndFeesPool.sol:63:        require(numOfVaults == _blsPublicKeysForKnots.length, "Inconsistent array lengths");
contracts/liquid-staking/GiantMevAndFeesPool.sol:87:        require(_stakingFundsVaults.length == _lpTokens.length, "Inconsistent array lengths");
contracts/liquid-staking/GiantMevAndFeesPool.sol:112:        require(numOfRotations > 0, "Empty arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:113:        require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:114:        require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:115:        require(numOfRotations == _amounts.length, "Inconsistent arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:116:        require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
contracts/liquid-staking/GiantMevAndFeesPool.sol:132:        require(numOfVaults > 0, "Empty arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:133:        require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:134:        require(numOfVaults == _amounts.length, "Inconsistent arrays");
contracts/liquid-staking/GiantMevAndFeesPool.sol:147:        require(msg.sender == address(lpTokenETH), "Caller is not giant LP");
contracts/liquid-staking/GiantMevAndFeesPool.sol:171:        require(msg.sender == address(lpTokenETH), "Caller is not giant LP");
contracts/liquid-staking/LPToken.sol:23:        require(msg.sender == deployer, "Only savETH vault");
contracts/liquid-staking/StakingFundsVault.sol:51:        require(msg.sender == address(liquidStakingNetworkManager), "Only network manager");
contracts/liquid-staking/StakingFundsVault.sol:71:        require(numOfValidators > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:72:        require(numOfValidators == _amounts.length, "Inconsistent array lengths");
contracts/liquid-staking/StakingFundsVault.sol:79:            require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
contracts/liquid-staking/StakingFundsVault.sol:80:            require(
contracts/liquid-staking/StakingFundsVault.sol:107:        require(msg.value == totalAmount, "Invalid ETH amount attached");
contracts/liquid-staking/StakingFundsVault.sol:114:        require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/StakingFundsVault.sol:115:        require(
contracts/liquid-staking/StakingFundsVault.sol:120:        require(msg.value == _amount, "Must provide correct amount of ETH");
contracts/liquid-staking/StakingFundsVault.sol:150:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:151:        require(numOfTokens == _amounts.length, "Inconsistent array length");
contracts/liquid-staking/StakingFundsVault.sol:154:            require(address(token) != address(0), "No ETH staked for specified BLS key");
contracts/liquid-staking/StakingFundsVault.sol:164:        require(numOfTokens > 0, "Empty arrays");
contracts/liquid-staking/StakingFundsVault.sol:165:        require(numOfTokens == _amounts.length, "Inconsistent array length");
contracts/liquid-staking/StakingFundsVault.sol:175:        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
contracts/liquid-staking/StakingFundsVault.sol:176:        require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");
contracts/liquid-staking/StakingFundsVault.sol:177:        require(address(_lpToken) != address(0), "Zero address specified");
contracts/liquid-staking/StakingFundsVault.sol:180:        require(
contracts/liquid-staking/StakingFundsVault.sol:184:        require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");
contracts/liquid-staking/StakingFundsVault.sol:191:        require(result, "Transfer failed");
contracts/liquid-staking/StakingFundsVault.sol:204:            require(
contracts/liquid-staking/StakingFundsVault.sol:210:            require(
contracts/liquid-staking/StakingFundsVault.sol:229:            require(address(token) != address(0), "Invalid BLS key");
contracts/liquid-staking/StakingFundsVault.sol:230:            require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
contracts/liquid-staking/StakingFundsVault.sol:240:        require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
contracts/liquid-staking/StakingFundsVault.sol:241:        require(_amount <= address(this).balance, "Not enough ETH to withdraw");
contracts/liquid-staking/StakingFundsVault.sol:242:        require(_wallet != address(0), "Zero address");
contracts/liquid-staking/StakingFundsVault.sol:245:        require(result, "Transfer failed");
contracts/liquid-staking/StakingFundsVault.sol:267:            require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
contracts/liquid-staking/StakingFundsVault.sol:320:            require(blsPubKey.length > 0, "Invalid token");
contracts/liquid-staking/StakingFundsVault.sol:346:            require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");
contracts/liquid-staking/StakingFundsVault.sol:361:        require(_syndicate != address(0), "Invalid configuration");
contracts/liquid-staking/StakingFundsVault.sol:372:        require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");
contracts/liquid-staking/StakingFundsVault.sol:373:        require(address(_lpTokenFactory) != address(0), "Zero Address");
contracts/liquid-staking/GiantLP.sol:30:        require(msg.sender == pool, "Only pool");
contracts/liquid-staking/GiantLP.sol:35:        require(msg.sender == pool, "Only pool");
contracts/liquid-staking/LSDNFactory.sol:51:        require(_liquidStakingManagerImplementation != address(0), "Zero Address");
contracts/liquid-staking/LSDNFactory.sol:52:        require(_syndicateFactory != address(0), "Zero Address");
contracts/liquid-staking/LSDNFactory.sol:53:        require(_lpTokenFactory != address(0), "Zero Address");
contracts/liquid-staking/LSDNFactory.sol:54:        require(_smartWalletFactory != address(0), "Zero Address");
contracts/liquid-staking/LSDNFactory.sol:55:        require(_brand != address(0), "Zero Address");
contracts/liquid-staking/LSDNFactory.sol:56:        require(_savETHVaultDeployer != address(0), "Zero Address");
contracts/liquid-staking/LSDNFactory.sol:57:        require(_stakingFundsVaultDeployer != address(0), "Zero Address");
contracts/liquid-staking/LSDNFactory.sol:58:        require(_optionalGatekeeperDeployer != address(0), "Zero Address");
contracts/liquid-staking/LiquidStakingManager.sol:161:        require(msg.sender == dao, "Must be DAO");
contracts/liquid-staking/LiquidStakingManager.sol:209:        require(smartWallet != address(0), "No wallet found");
contracts/liquid-staking/LiquidStakingManager.sol:240:        require(_newAddress != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:241:        require(_newAddress != dao, "Same address");
contracts/liquid-staking/LiquidStakingManager.sol:250:        require(_commissionPercentage != daoCommissionPercentage, "Same commission percentage");
contracts/liquid-staking/LiquidStakingManager.sol:256:        require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
contracts/liquid-staking/LiquidStakingManager.sol:257:        require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
contracts/liquid-staking/LiquidStakingManager.sol:258:        require(numberOfKnots == 0, "Cannot change ticker once house is created");
contracts/liquid-staking/LiquidStakingManager.sol:268:        require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
contracts/liquid-staking/LiquidStakingManager.sol:279:        require(_nodeRunner != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:280:        require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
contracts/liquid-staking/LiquidStakingManager.sol:290:        require(_newRepresentative != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:291:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:294:        require(smartWallet != address(0), "No smart wallet");
contracts/liquid-staking/LiquidStakingManager.sol:295:        require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
contracts/liquid-staking/LiquidStakingManager.sol:296:        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");
contracts/liquid-staking/LiquidStakingManager.sol:309:        require(_newRepresentative != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:312:        require(smartWallet != address(0), "No smart wallet");
contracts/liquid-staking/LiquidStakingManager.sol:313:        require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
contracts/liquid-staking/LiquidStakingManager.sol:314:        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");
contracts/liquid-staking/LiquidStakingManager.sol:327:        require(_recipient != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:328:        require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:331:        require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");
contracts/liquid-staking/LiquidStakingManager.sol:332:        require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:333:        require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");
contracts/liquid-staking/LiquidStakingManager.sol:334:        require(
contracts/liquid-staking/LiquidStakingManager.sol:357:        require(_new != address(0) && _current != _new, "New is zero or current");
contracts/liquid-staking/LiquidStakingManager.sol:360:        require(wallet != address(0), "Wallet does not exist");
contracts/liquid-staking/LiquidStakingManager.sol:361:        require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");
contracts/liquid-staking/LiquidStakingManager.sol:364:        require(newRunnerCurrentWallet == address(0), "New runner has a wallet");
contracts/liquid-staking/LiquidStakingManager.sol:386:        require(_blsPubKeys.length > 0, "No BLS keys specified");
contracts/liquid-staking/LiquidStakingManager.sol:387:        require(_recipient != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:390:        require(smartWallet != address(0), "Unknown node runner");
contracts/liquid-staking/LiquidStakingManager.sol:393:            require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:396:            require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");
contracts/liquid-staking/LiquidStakingManager.sol:412:        require(transferResult, "Failed to transfer");
contracts/liquid-staking/LiquidStakingManager.sol:416:            require(transferResult, "Failed to transfer");
contracts/liquid-staking/LiquidStakingManager.sol:432:        require(len >= 1, "No value provided");
contracts/liquid-staking/LiquidStakingManager.sol:433:        require(len == _blsSignatures.length, "Unequal number of array values");
contracts/liquid-staking/LiquidStakingManager.sol:434:        require(msg.value == len * 4 ether, "Insufficient ether provided");
contracts/liquid-staking/LiquidStakingManager.sol:435:        require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");
contracts/liquid-staking/LiquidStakingManager.sol:436:        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
contracts/liquid-staking/LiquidStakingManager.sol:437:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:455:            require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
contracts/liquid-staking/LiquidStakingManager.sol:461:            require(result, "Transfer failed");
contracts/liquid-staking/LiquidStakingManager.sol:469:            require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:471:            require(
contracts/liquid-staking/LiquidStakingManager.sol:532:        require(numOfValidators > 0, "No data");
contracts/liquid-staking/LiquidStakingManager.sol:533:        require(numOfValidators == _ciphertexts.length, "Inconsistent array lengths");
contracts/liquid-staking/LiquidStakingManager.sol:534:        require(numOfValidators == _aesEncryptorKeys.length, "Inconsistent array lengths");
contracts/liquid-staking/LiquidStakingManager.sol:535:        require(numOfValidators == _encryptionSignatures.length, "Inconsistent array lengths");
contracts/liquid-staking/LiquidStakingManager.sol:536:        require(numOfValidators == _dataRoots.length, "Inconsistent array lengths");
contracts/liquid-staking/LiquidStakingManager.sol:541:            require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:544:            require(associatedSmartWallet != address(0), "Unknown BLS public key");
contracts/liquid-staking/LiquidStakingManager.sol:545:            require(
contracts/liquid-staking/LiquidStakingManager.sol:583:        require(numOfKnotsToProcess > 0, "Empty array");
contracts/liquid-staking/LiquidStakingManager.sol:584:        require(numOfKnotsToProcess == _beaconChainBalanceReports.length, "Inconsistent array lengths");
contracts/liquid-staking/LiquidStakingManager.sol:585:        require(numOfKnotsToProcess == _reportSignatures.length, "Inconsistent array lengths");
contracts/liquid-staking/LiquidStakingManager.sol:589:            require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
contracts/liquid-staking/LiquidStakingManager.sol:592:            require(
contracts/liquid-staking/LiquidStakingManager.sol:658:        require(_dao != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:659:        require(_syndicateFactory != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:660:        require(_smartWalletFactory != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:661:        require(_brand != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:662:        require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
contracts/liquid-staking/LiquidStakingManager.sol:663:        require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
contracts/liquid-staking/LiquidStakingManager.sol:685:        require(_nodeRunner != address(0), "Zero address");
contracts/liquid-staking/LiquidStakingManager.sol:688:            require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
contracts/liquid-staking/LiquidStakingManager.sol:922:        require(_received > 0, "Nothing received");
contracts/liquid-staking/LiquidStakingManager.sol:936:        require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
contracts/liquid-staking/LiquidStakingManager.sol:939:        require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
contracts/liquid-staking/LiquidStakingManager.sol:940:        require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
contracts/liquid-staking/LiquidStakingManager.sol:943:        require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");
contracts/liquid-staking/LiquidStakingManager.sol:944:        require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
contracts/liquid-staking/LiquidStakingManager.sol:949:        require(_commissionPercentage <= MODULO, "Invalid commission");
contracts/liquid-staking/ETHPoolLPFactory.sol:59:        require(numOfRotations > 0, "Empty arrays");
contracts/liquid-staking/ETHPoolLPFactory.sol:60:        require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
contracts/liquid-staking/ETHPoolLPFactory.sol:61:        require(numOfRotations == _amounts.length, "Inconsistent arrays");
contracts/liquid-staking/ETHPoolLPFactory.sol:77:        require(address(_oldLPToken) != address(0), "Zero address");
contracts/liquid-staking/ETHPoolLPFactory.sol:78:        require(address(_newLPToken) != address(0), "Zero address");
contracts/liquid-staking/ETHPoolLPFactory.sol:79:        require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
contracts/liquid-staking/ETHPoolLPFactory.sol:80:        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
contracts/liquid-staking/ETHPoolLPFactory.sol:81:        require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
contracts/liquid-staking/ETHPoolLPFactory.sol:82:        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
contracts/liquid-staking/ETHPoolLPFactory.sol:83:        require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");
contracts/liquid-staking/ETHPoolLPFactory.sol:88:        require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");
contracts/liquid-staking/ETHPoolLPFactory.sol:89:        require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");
contracts/liquid-staking/ETHPoolLPFactory.sol:91:        require(
contracts/liquid-staking/ETHPoolLPFactory.sol:96:        require(
contracts/liquid-staking/ETHPoolLPFactory.sol:111:        require(_amount >= MIN_STAKING_AMOUNT, "Min amount not reached");
contracts/liquid-staking/ETHPoolLPFactory.sol:112:        require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");
contracts/liquid-staking/ETHPoolLPFactory.sol:122:            require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
contracts/liquid-staking/ETHPoolLPFactory.sol:130:            require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
contracts/liquid-staking/SyndicateRewardsProcessor.sol:57:        require(_recipient != address(0), "Zero address");
contracts/liquid-staking/SyndicateRewardsProcessor.sol:68:                require(success, "Failed to transfer");
contracts/liquid-staking/GiantSavETHVaultPool.sol:36:        require(numOfSavETHVaults > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:37:        require(numOfSavETHVaults == _ETHTransactionAmounts.length, "Inconsistent array lengths");
contracts/liquid-staking/GiantSavETHVaultPool.sol:38:        require(numOfSavETHVaults == _blsPublicKeys.length, "Inconsistent array lengths");
contracts/liquid-staking/GiantSavETHVaultPool.sol:39:        require(numOfSavETHVaults == _stakeAmounts.length, "Inconsistent array lengths");
contracts/liquid-staking/GiantSavETHVaultPool.sol:49:            require(
contracts/liquid-staking/GiantSavETHVaultPool.sol:72:        require(numOfVaults > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:73:        require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:74:        require(numOfVaults == _amounts.length, "Inconsistent arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:89:                require(vault.isDETHReadyForWithdrawal(address(token)), "dETH is not ready for withdrawal");
contracts/liquid-staking/GiantSavETHVaultPool.sol:92:                require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");
contracts/liquid-staking/GiantSavETHVaultPool.sol:123:        require(numOfRotations > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:124:        require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:125:        require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:126:        require(numOfRotations == _amounts.length, "Inconsistent arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:127:        require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
contracts/liquid-staking/GiantSavETHVaultPool.sol:143:        require(numOfVaults > 0, "Empty arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:144:        require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:145:        require(numOfVaults == _amounts.length, "Inconsistent arrays");
contracts/liquid-staking/GiantSavETHVaultPool.sol:149:                require(
contracts/liquid-staking/LPTokenFactory.sol:19:        require(_lpTokenImplementation != address(0), "Address cannot be zero");
contracts/liquid-staking/LPTokenFactory.sol:33:        require(address(_deployer) != address(0), "Zero address");
contracts/liquid-staking/LPTokenFactory.sol:34:        require(bytes(_tokenSymbol).length != 0, "Symbol cannot be zero");
contracts/liquid-staking/LPTokenFactory.sol:35:        require(bytes(_tokenName).length != 0, "Name cannot be zero");
```


## `++i`/`i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops :
```solidity
contracts/syndicate/Syndicate.sol:211:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:259:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:301:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:346:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:420:        for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
contracts/syndicate/Syndicate.sol:513:                    for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
contracts/syndicate/Syndicate.sol:560:        for (uint256 i; i < knotsToRegister; ++i) {
contracts/syndicate/Syndicate.sol:585:        for (uint256 i; i < _priorityStakers.length; ++i) {
contracts/syndicate/Syndicate.sol:598:        for (uint256 i; i < _blsPublicKeys.length; ++i) {
contracts/syndicate/Syndicate.sol:648:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/liquid-staking/GiantPoolBase.sol:76:        for (uint256 i; i < amountOfTokens; ++i) {
contracts/liquid-staking/SavETHVault.sol:63:        for (uint256 i; i < numOfValidators; ++i) {
contracts/liquid-staking/SavETHVault.sol:103:        for (uint256 i; i < numOfTokens; ++i) {
contracts/liquid-staking/SavETHVault.sol:116:        for (uint256 i; i < numOfTokens; ++i) {
contracts/liquid-staking/GiantMevAndFeesPool.sol:38:        for (uint256 i; i < numOfVaults; ++i) {
contracts/liquid-staking/GiantMevAndFeesPool.sol:64:        for (uint256 i; i < numOfVaults; ++i) {
contracts/liquid-staking/GiantMevAndFeesPool.sol:90:        for (uint256 i; i < _stakingFundsVaults.length; ++i) {
contracts/liquid-staking/GiantMevAndFeesPool.sol:117:        for (uint256 i; i < numOfRotations; ++i) {
contracts/liquid-staking/GiantMevAndFeesPool.sol:135:        for (uint256 i; i < numOfVaults; ++i) {
contracts/liquid-staking/GiantMevAndFeesPool.sol:183:        for (uint256 i; i < _lpTokens.length; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:78:        for (uint256 i; i < numOfValidators; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:152:        for (uint256 i; i < numOfTokens; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:166:        for (uint256 i; i < numOfTokens; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:203:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:266:        for (uint256 i; i < _blsPublicKeys.length; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:276:        for (uint256 i; i < _blsPubKeys.length; ++i) {
contracts/liquid-staking/StakingFundsVault.sol:286:        for (uint256 i; i < _token.length; ++i) {
contracts/liquid-staking/LiquidStakingManager.sol:392:        for(uint256 i; i < _blsPubKeys.length; ++i) {
contracts/liquid-staking/LiquidStakingManager.sol:465:        for(uint256 i; i < len; ++i) {
contracts/liquid-staking/LiquidStakingManager.sol:538:        for (uint256 i; i < numOfValidators; ++i) {
contracts/liquid-staking/LiquidStakingManager.sol:587:        for (uint256 i; i < numOfKnotsToProcess; ++i) {
contracts/liquid-staking/ETHPoolLPFactory.sol:63:        for (uint256 i; i < numOfRotations; ++i) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:42:        for (uint256 i; i < numOfSavETHVaults; ++i) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:78:        for (uint256 i; i < numOfVaults; ++i) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:82:            for (uint256 j; j < _lpTokens[i].length; ++j) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:128:        for (uint256 i; i < numOfRotations; ++i) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:146:        for (uint256 i; i < numOfVaults; ++i) {
contracts/liquid-staking/GiantSavETHVaultPool.sol:148:            for (uint256 j; j < _lpTokens[i].length; ++j) {
```

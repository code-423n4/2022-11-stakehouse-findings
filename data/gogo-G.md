# 2022-11-STAKEHOUSE

## Gas Optimizations Report

### The usage of `++i` will cost less gas than `i++`. The same change can be applied to `i--` as well.

This change would save up to 6 gas per instance/loop.

_There are **1** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

144:      numberOfLPTokensIssued++;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

### State variables should be cached in stack variables rather than re-reading them.

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_There are **4** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

      /// @audit Cache `KnotAssociatedWithLPToken`. Used 3 times in `rotateLPTokens`
85:   bytes memory blsPublicKeyOfPreviousKnot = KnotAssociatedWithLPToken[_oldLPToken];
86:   bytes memory blsPublicKeyOfNewKnot = KnotAssociatedWithLPToken[_newLPToken];
106:  emit LPTokenMinted(KnotAssociatedWithLPToken[_newLPToken], address(_newLPToken), msg.sender, _amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

      /// @audit Cache `stakehouse`. Used 4 times in `_createLSDNStakehouse`
843:  IERC20 sETH = IERC20(getSlotRegistry().stakeHouseShareTokens(stakehouse));
847:      stakehouse,
855:      IStakeHouseRegistry(stakehouse).setGateKeeper(address(gatekeeper));
875:  emit StakehouseCreated(stakehouseTicker, stakehouse);

      /// @audit Cache `enableWhitelisting`. Used 3 times in `updateWhitelisting`
268:  require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
270:  emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);
272:  return enableWhitelisting;

      /// @audit Cache `smartWalletOfNodeRunner`. Used 3 times in `rotateNodeRunnerOfSmartWallet`
359:  address wallet = smartWalletOfNodeRunner[_current];
363:  address newRunnerCurrentWallet = smartWalletOfNodeRunner[_new];
369:  delete smartWalletOfNodeRunner[_current];
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

### Using `!= 0` on `uints` costs less gas than `> 0`.

This change saves 3 gas per instance/loop

_There are **29** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

59:    require(numOfRotations > 0, "Empty arrays");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

35:    require(numOfVaults > 0, "Zero vaults");

62:    require(numOfVaults > 0, "Empty array");

112:  require(numOfRotations > 0, "Empty arrays");

132:  require(numOfVaults > 0, "Empty arrays");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

71:    require(amountOfTokens > 0, "Empty arrays");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

36:    require(numOfSavETHVaults > 0, "Empty arrays");

72:    require(numOfVaults > 0, "Empty arrays");

123:  require(numOfRotations > 0, "Empty arrays");

143:  require(numOfVaults > 0, "Empty arrays");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

414:  if (daoAmount > 0) {

532:  require(numOfValidators > 0, "No data");

583:  require(numOfKnotsToProcess > 0, "Empty array");

922:  require(_received > 0, "Nothing received");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/SavETHVault.sol

59:    require(numOfValidators > 0, "Empty arrays");

101:  require(numOfTokens > 0, "Empty arrays");

114:  require(numOfTokens > 0, "Empty arrays");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

71:    require(numOfValidators > 0, "Empty arrays");

150:  require(numOfTokens > 0, "Empty arrays");

164:  require(numOfTokens > 0, "Empty arrays");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

37:    if (_balanceOfSender > 0) {

59:    if (balance > 0) {

62:        if (due > 0) {

77:    if (_numOfShares > 0) {

81:        if (unprocessed > 0) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```solidity
File: contracts/syndicate/Syndicate.sol

315:      if (unclaimedUserShare > 0) {

500:  if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {

588:      if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();

656:      if (unclaimedUserShare > 0) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

### Using `private` rather than `public` for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

_There are **4** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

38:   uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

22:   uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

15:   uint256 public constant PRECISION = 1e24;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```solidity
File: contracts/syndicate/Syndicate.sol

66:   uint256 public constant PRECISION = 1e24;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

### Functions that are access-restricted from most users may be marked as `payable`

Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **17** instances of this issue:_

```solidity
File: contracts/liquid-staking/GiantLP.sol

29:   function mint(address _recipient, uint256 _amount) external {

34:   function burn(address _recipient, uint256 _amount) external {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

146:  function beforeTokenTransfer(address _from, address _to, uint256) external {

170:  function afterTokenTransfer(address, address _to, uint256) external {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

218:  function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {

239:  function updateDAOAddress(address _newAddress) external onlyDAO {

249:  function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {

255:  function updateTicker(string calldata _newTicker) external onlyDAO {

267:  function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {

278:  function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {

308:  function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

56:   function updateDerivativesMinted() external onlyManager {

239:  function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

110:  function setApproval(address to, bool status) external override onlyOwner {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol

```solidity
File: contracts/syndicate/Syndicate.sol

154:  function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {

161:  function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {

168:  function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops

When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **38** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

63:    for (uint256 i; i < numOfRotations; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

38:    for (uint256 i; i < numOfVaults; ++i) {

64:    for (uint256 i; i < numOfVaults; ++i) {

90:    for (uint256 i; i < _stakingFundsVaults.length; ++i) {

117:  for (uint256 i; i < numOfRotations; ++i) {

135:  for (uint256 i; i < numOfVaults; ++i) {

183:  for (uint256 i; i < _lpTokens.length; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

76:    for (uint256 i; i < amountOfTokens; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

42:    for (uint256 i; i < numOfSavETHVaults; ++i) {

78:    for (uint256 i; i < numOfVaults; ++i) {

82:        for (uint256 j; j < _lpTokens[i].length; ++j) {

128:  for (uint256 i; i < numOfRotations; ++i) {

146:  for (uint256 i; i < numOfVaults; ++i) {

148:      for (uint256 j; j < _lpTokens[i].length; ++j) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

392:  for(uint256 i; i < _blsPubKeys.length; ++i) {

465:  for(uint256 i; i < len; ++i) {

538:  for (uint256 i; i < numOfValidators; ++i) {

587:  for (uint256 i; i < numOfKnotsToProcess; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/SavETHVault.sol

63:    for (uint256 i; i < numOfValidators; ++i) {

103:  for (uint256 i; i < numOfTokens; ++i) {

116:  for (uint256 i; i < numOfTokens; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

78:    for (uint256 i; i < numOfValidators; ++i) {

152:  for (uint256 i; i < numOfTokens; ++i) {

166:  for (uint256 i; i < numOfTokens; ++i) {

203:  for (uint256 i; i < _blsPubKeys.length; ++i) {

266:  for (uint256 i; i < _blsPublicKeys.length; ++i) {

276:  for (uint256 i; i < _blsPubKeys.length; ++i) {

286:  for (uint256 i; i < _token.length; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/syndicate/Syndicate.sol

211:  for (uint256 i; i < _blsPubKeys.length; ++i) {

259:  for (uint256 i; i < _blsPubKeys.length; ++i) {

301:  for (uint256 i; i < _blsPubKeys.length; ++i) {

346:  for (uint256 i; i < _blsPubKeys.length; ++i) {

420:  for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

513:              for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

560:  for (uint256 i; i < knotsToRegister; ++i) {

585:  for (uint256 i; i < _priorityStakers.length; ++i) {

598:  for (uint256 i; i < _blsPublicKeys.length; ++i) {

648:  for (uint256 i; i < _blsPubKeys.length; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

### `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

_There are **24** instances of this issue:_

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

39:    idleETH += msg.value;

57:    idleETH -= _amount;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

615:      stakedKnotsOfSmartWallet[smartWallet] -= 1;

770:  stakedKnotsOfSmartWallet[smartWallet] += 1;

782:  numberOfKnots += 1;

839:  numberOfKnots += 1;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

58:    totalShares += 4 ether;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

85:            accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;

65:            totalClaimed += due;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```solidity
File: contracts/syndicate/Syndicate.sol

185:          accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);

190:      accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;

225:      totalFreeFloatingShares += _sETHAmount;

269:          totalFreeFloatingShares -= _sETHAmount;

621:  totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];

317:          totalClaimed += unclaimedUserShare;

658:          totalClaimed += unclaimedUserShare;

558:  numberOfRegisteredKnots += knotsToRegister;

624:  numberOfRegisteredKnots -= 1;

226:      sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;

272:      sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;

227:      sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;

273:      sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;

511:              accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;

521:                  accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

### Don't compare boolean expressions to boolean literals

Use `if(x)`/`if(!x)` instead of `if(x == true)`/`if(x == false)`.

_There are **18** instances of this issue:_

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

150:              vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

291:  require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

328:  require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

332:  require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

393:      require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

436:  require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

437:  require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

469:      require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

541:      require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

589:      require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

688:      require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/SavETHVault.sol

64:        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

84:    require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

79:        require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

114:  require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

205:          liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/syndicate/Syndicate.sol

611:  if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();

612:  if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

### Multiplication/division by a number thats a power of 2 should use bit shifting

`x * 2` is equivalent to `x << 1` and `x / 2` is the same as `x >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

_There are **2** instances of this issue:_

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

434:  require(msg.value == len * 4 ether, "Insufficient ether provided");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/syndicate/Syndicate.sol

546:  return (_ethSinceLastUpdate * PRECISION) / (numberOfRegisteredKnots * 4 ether);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

### Splitting `require()` statements that use `&&` saves gas

Instead of using `&&` on single `require` check using two `require` checks can save gas

_There are **1** instances of this issue:_

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

357:  require(_new != address(0) && _current != _new, "New is zero or current");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

### Use `calldata` instead of `memory` for function parameters

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **18** instances of this issue:_

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

879:  function _autoStakeWithSyndicate(address _associatedSmartWallet, bytes memory _blsPubKey) internal {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

355:  function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {

360:  function _claimFundsFromSyndicateForDistribution(address _syndicate, bytes[] memory _blsPubKeys) internal {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

41:   function execute(address target, bytes memory callData)

54:    bytes memory callData,

69:    bytes memory callData,
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol

```solidity
File: contracts/syndicate/Syndicate.sol

132:  address[] memory _priorityStakers,

133:  bytes[] memory _blsPubKeysForSyndicateKnots

337:  function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {

343:  function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {

359:  function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {

472:  address[] memory _priorityStakers,

473:  bytes[] memory _blsPubKeysForSyndicateKnots

491:  function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {

555:  function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots) internal {

583:  function _addPriorityStakers(address[] memory _priorityStakers) internal {

610:  function _deRegisterKnot(bytes memory _blsPublicKey) internal {

631:  bytes memory _blsPublicKey
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **32** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

80:    require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

81:    require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");

83:    require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

111:  require(_amount >= MIN_STAKING_AMOUNT, "Min amount not reached");

122:      require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

130:      require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

116:  require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

35:    require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");

53:    require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

54:    require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");

55:    require(idleETH >= _amount, "Come back later or withdraw less ETH");

94:    require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

95:    require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

92:            require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");

127:  require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

256:  require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

257:  require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

333:  require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");

432:  require(len >= 1, "No value provided");

662:  require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");

663:  require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

936:  require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

949:  require(_commissionPercentage <= MODULO, "Invalid commission");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/SavETHVault.sol

127:  require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

128:  require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

161:          require(dETHBalance >= 24 ether, "Nothing to withdraw");

204:  require(_amount >= 24 ether, "Amount cannot be less than 24 ether");

205:  require(address(this).balance >= _amount, "Insufficient withdrawal amount");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

175:  require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

176:  require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

240:  require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

241:  require(_amount <= address(this).balance, "Not enough ETH to withdraw");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

### Tight variable packing

Solidity contracts have contiguous 32 bytes (256 bits) slots used in storage. By arranging the variables, it is possible to minimize the number of slots used within a contract’s storage and therefore reduce deployment costs.

_There are **1** instances of this issue:_

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

90:   /// @audit Currently storage variables are packed in 23 slots but could fit in 22
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

### Use `immutable` & `constant` for state variables that do not change their value

_There are **24** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

28:   string internal baseLPTokenName;

29:   string internal baseLPTokenSymbol;

35:   uint256 public maxStakingAmountPerValidator;

41:   LPTokenFactory public lpTokenFactory;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/GiantLP.sol

11:   address public pool;

14:   ITransferHookProcessor public transferHookProcessor;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

28:   GiantLP public lpTokenETH;

31:   LSDNFactory public liquidStakingDerivativeFactory;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/LPToken.sol

14:   address public deployer;

17:   ITransferHookProcessor transferHookProcessor;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

15:   address public lpTokenImplementation;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPTokenFactory.sol

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

15:   address public liquidStakingManagerImplementation;

18:   address public syndicateFactory;

21:   address public lpTokenFactory;

24:   address public smartWalletFactory;

27:   address public brand;

30:   address public savETHVaultDeployer;

33:   address public stakingFundsVaultDeployer;

36:   address public optionalGatekeeperDeployer;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol

```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol

12:   ILiquidStakingManager public liquidStakingManager;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

13:   address implementation;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVaultDeployer.sol

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

13:   address implementation;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

18:   uint256 public accumulatedETHPerLPShare;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```solidity
File: contracts/syndicate/SyndicateFactory.sol

13:   address public syndicateImplementation;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol

### `require()/revert()` strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

_There are **40** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

122:      require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

130:      require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

55:    require(idleETH >= _amount, "Come back later or withdraw less ETH");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

149:          require(
150:              vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
151:              "ETH is either staked or derivatives minted"
152:          );
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

256:  require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

257:  require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

258:  require(numberOfKnots == 0, "Cannot change ticker once house is created");

268:  require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");

280:  require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

291:  require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

328:  require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

331:  require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");

332:  require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

393:      require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

396:      require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

435:  require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");

437:  require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

455:      require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");

469:      require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

541:      require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

589:      require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

662:  require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");

663:  require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

936:  require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

939:  require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");

940:  require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

944:  require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/SavETHVault.sol

64:        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

84:    require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

90:    require(msg.value == _amount, "Must provide correct amount of ETH");

204:  require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

79:        require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

114:  require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

120:  require(msg.value == _amount, "Must provide correct amount of ETH");

154:      require(address(token) != address(0), "No ETH staked for specified BLS key");

240:  require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

267:      require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

33:    require(
34:        initialOwner != address(0),
35:        "OwnableSmartWallet: Attempting to initialize with zero address owner"
36:    );

96:    require(
97:        isTransferApproved(owner(), msg.sender),
98:        "OwnableSmartWallet: Transfer is not allowed"
99:     );

111:  require(
112:      to != address(0),
113:      "OwnableSmartWallet: Approval cannot be set for zero address"
114:   );
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol

### Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

_There are **223** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

59:    require(numOfRotations > 0, "Empty arrays");

60:    require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

61:    require(numOfRotations == _amounts.length, "Inconsistent arrays");

77:    require(address(_oldLPToken) != address(0), "Zero address");

78:    require(address(_newLPToken) != address(0), "Zero address");

79:    require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");

80:    require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

81:    require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");

82:    require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");

83:    require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

88:    require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");

89:    require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");

91:    require(
92:        getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfPreviousKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
93:        "Lifecycle status must be one"
94:    );

96:    require(
97:        getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
98:        "Lifecycle status must be one"
99:    );

111:  require(_amount >= MIN_STAKING_AMOUNT, "Min amount not reached");

112:  require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");

122:      require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

130:      require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/GiantLP.sol

30:    require(msg.sender == pool, "Only pool");

35:    require(msg.sender == pool, "Only pool");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

35:    require(numOfVaults > 0, "Zero vaults");

36:    require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");

37:    require(numOfVaults == _amounts.length, "Inconsistent lengths");

43:        require(
44:            liquidStakingDerivativeFactory.isLiquidStakingManager(address(sfv.liquidStakingNetworkManager())),
45:            "Invalid liquid staking manager"
46:        );

62:    require(numOfVaults > 0, "Empty array");

63:    require(numOfVaults == _blsPublicKeysForKnots.length, "Inconsistent array lengths");

87:    require(_stakingFundsVaults.length == _lpTokens.length, "Inconsistent array lengths");

112:  require(numOfRotations > 0, "Empty arrays");

113:  require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");

114:  require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

115:  require(numOfRotations == _amounts.length, "Inconsistent arrays");

116:  require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

132:  require(numOfVaults > 0, "Empty arrays");

133:  require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

134:  require(numOfVaults == _amounts.length, "Inconsistent arrays");

147:  require(msg.sender == address(lpTokenETH), "Caller is not giant LP");

171:  require(msg.sender == address(lpTokenETH), "Caller is not giant LP");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

35:    require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");

36:    require(msg.value == _amount, "Value equal to amount");

53:    require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

54:    require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");

55:    require(idleETH >= _amount, "Come back later or withdraw less ETH");

61:    require(success, "Failed to transfer ETH");

71:    require(amountOfTokens > 0, "Empty arrays");

72:    require(amountOfTokens == _amounts.length, "Inconsistent array lengths");

94:    require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

95:    require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");

96:    require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

36:    require(numOfSavETHVaults > 0, "Empty arrays");

37:    require(numOfSavETHVaults == _ETHTransactionAmounts.length, "Inconsistent array lengths");

38:    require(numOfSavETHVaults == _blsPublicKeys.length, "Inconsistent array lengths");

39:    require(numOfSavETHVaults == _stakeAmounts.length, "Inconsistent array lengths");

49:        require(
50:            liquidStakingDerivativeFactory.isLiquidStakingManager(address(savETHPool.liquidStakingManager())),
51:            "Invalid liquid staking manager"
52:        );

72:    require(numOfVaults > 0, "Empty arrays");

73:    require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

74:    require(numOfVaults == _amounts.length, "Inconsistent arrays");

89:            require(vault.isDETHReadyForWithdrawal(address(token)), "dETH is not ready for withdrawal");

92:            require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");

123:  require(numOfRotations > 0, "Empty arrays");

124:  require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");

125:  require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

126:  require(numOfRotations == _amounts.length, "Inconsistent arrays");

127:  require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

143:  require(numOfVaults > 0, "Empty arrays");

144:  require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

145:  require(numOfVaults == _amounts.length, "Inconsistent arrays");

149:          require(
150:              vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
151:              "ETH is either staked or derivatives minted"
152:          );
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```solidity
File: contracts/liquid-staking/LPToken.sol

23:    require(msg.sender == deployer, "Only savETH vault");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

19:    require(_lpTokenImplementation != address(0), "Address cannot be zero");

33:    require(address(_deployer) != address(0), "Zero address");

34:    require(bytes(_tokenSymbol).length != 0, "Symbol cannot be zero");

35:    require(bytes(_tokenName).length != 0, "Name cannot be zero");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPTokenFactory.sol

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

51:    require(_liquidStakingManagerImplementation != address(0), "Zero Address");

52:    require(_syndicateFactory != address(0), "Zero Address");

53:    require(_lpTokenFactory != address(0), "Zero Address");

54:    require(_smartWalletFactory != address(0), "Zero Address");

55:    require(_brand != address(0), "Zero Address");

56:    require(_savETHVaultDeployer != address(0), "Zero Address");

57:    require(_stakingFundsVaultDeployer != address(0), "Zero Address");

58:    require(_optionalGatekeeperDeployer != address(0), "Zero Address");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

161:  require(msg.sender == dao, "Must be DAO");

209:  require(smartWallet != address(0), "No wallet found");

240:  require(_newAddress != address(0), "Zero address");

241:  require(_newAddress != dao, "Same address");

250:  require(_commissionPercentage != daoCommissionPercentage, "Same commission percentage");

256:  require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

257:  require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

258:  require(numberOfKnots == 0, "Cannot change ticker once house is created");

268:  require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");

279:  require(_nodeRunner != address(0), "Zero address");

280:  require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

290:  require(_newRepresentative != address(0), "Zero address");

291:  require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

294:  require(smartWallet != address(0), "No smart wallet");

295:  require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");

296:  require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

309:  require(_newRepresentative != address(0), "Zero address");

312:  require(smartWallet != address(0), "No smart wallet");

313:  require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");

314:  require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

327:  require(_recipient != address(0), "Zero address");

328:  require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

331:  require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");

332:  require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

333:  require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");

334:  require(
335:      getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
336:      "Initials not registered"
337:  );

357:  require(_new != address(0) && _current != _new, "New is zero or current");

360:  require(wallet != address(0), "Wallet does not exist");

361:  require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");

364:  require(newRunnerCurrentWallet == address(0), "New runner has a wallet");

386:  require(_blsPubKeys.length > 0, "No BLS keys specified");

387:  require(_recipient != address(0), "Zero address");

390:  require(smartWallet != address(0), "Unknown node runner");

393:      require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

396:      require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

412:  require(transferResult, "Failed to transfer");

416:      require(transferResult, "Failed to transfer");

432:  require(len >= 1, "No value provided");

433:  require(len == _blsSignatures.length, "Unequal number of array values");

434:  require(msg.value == len * 4 ether, "Insufficient ether provided");

435:  require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");

436:  require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

437:  require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

455:      require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");

461:      require(result, "Transfer failed");

469:      require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

471:      require(
472:          getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKey) == IDataStructures.LifecycleStatus.UNBEGUN,
473:          "Lifecycle status must be zero"
474:      );

532:  require(numOfValidators > 0, "No data");

533:  require(numOfValidators == _ciphertexts.length, "Inconsistent array lengths");

534:  require(numOfValidators == _aesEncryptorKeys.length, "Inconsistent array lengths");

535:  require(numOfValidators == _encryptionSignatures.length, "Inconsistent array lengths");

536:  require(numOfValidators == _dataRoots.length, "Inconsistent array lengths");

541:      require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

544:      require(associatedSmartWallet != address(0), "Unknown BLS public key");

545:      require(
546:          getAccountManager().blsPublicKeyToLifecycleStatus(blsPubKey) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
547:          "Initials not registered"
548:      );

583:  require(numOfKnotsToProcess > 0, "Empty array");

584:  require(numOfKnotsToProcess == _beaconChainBalanceReports.length, "Inconsistent array lengths");

585:  require(numOfKnotsToProcess == _reportSignatures.length, "Inconsistent array lengths");

589:      require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

592:      require(
593:          getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.DEPOSIT_COMPLETED,
594:          "Lifecycle status must be two"
595:      );

658:  require(_dao != address(0), "Zero address");

659:  require(_syndicateFactory != address(0), "Zero address");

660:  require(_smartWalletFactory != address(0), "Zero address");

661:  require(_brand != address(0), "Zero address");

662:  require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");

663:  require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

685:  require(_nodeRunner != address(0), "Zero address");

688:      require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

734:      revert("Unexpected state");

922:  require(_received > 0, "Nothing received");

936:  require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

939:  require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");

940:  require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

943:  require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");

944:  require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");

949:  require(_commissionPercentage <= MODULO, "Invalid commission");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/SavETHVault.sol

50:    require(msg.sender == address(liquidStakingManager), "Not the savETH vault manager");

59:    require(numOfValidators > 0, "Empty arrays");

60:    require(numOfValidators == _amounts.length, "Inconsistent array lengths");

64:        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

65:        require(
66:            getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
67:            "Lifecycle status must be one"
68:        );

76:    require(msg.value == totalAmount, "Invalid ETH amount attached");

84:    require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

85:    require(
86:        getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
87:        "Lifecycle status must be one"
88:    );

90:    require(msg.value == _amount, "Must provide correct amount of ETH");

101:  require(numOfTokens > 0, "Empty arrays");

102:  require(numOfTokens == _amounts.length, "Inconsistent array length");

114:  require(numOfTokens > 0, "Empty arrays");

115:  require(numOfTokens == _amounts.length, "Inconsisent array length");

127:  require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

128:  require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

134:  require(
135:      validatorStatus == IDataStructures.LifecycleStatus.INITIALS_REGISTERED ||
136:      validatorStatus == IDataStructures.LifecycleStatus.TOKENS_MINTED,
137:      "Cannot burn LP tokens"
138:  );

161:          require(dETHBalance >= 24 ether, "Nothing to withdraw");

186:  require(isStaleLiquidity, "Liquidity is still fresh");

190:  require(result, "Transfer failed");

204:  require(_amount >= 24 ether, "Amount cannot be less than 24 ether");

205:  require(address(this).balance >= _amount, "Insufficient withdrawal amount");

206:  require(_smartWallet != address(0), "Zero address");

207:  require(_smartWallet != address(this), "This address");

210:  require(result, "Transfer failed");

236:  require(_liquidStakingManagerAddress != address(0), "Zero address");

237:  require(address(_lpTokenFactory) != address(0), "Zero address");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

51:    require(msg.sender == address(liquidStakingNetworkManager), "Only network manager");

71:    require(numOfValidators > 0, "Empty arrays");

72:    require(numOfValidators == _amounts.length, "Inconsistent array lengths");

79:        require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

80:        require(
81:            getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
82:            "Lifecycle status must be one"
83:        );

107:  require(msg.value == totalAmount, "Invalid ETH amount attached");

114:  require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

115:  require(
116:      getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
117:      "Lifecycle status must be one"
118:  );

120:  require(msg.value == _amount, "Must provide correct amount of ETH");

150:  require(numOfTokens > 0, "Empty arrays");

151:  require(numOfTokens == _amounts.length, "Inconsistent array length");

154:      require(address(token) != address(0), "No ETH staked for specified BLS key");

164:  require(numOfTokens > 0, "Empty arrays");

165:  require(numOfTokens == _amounts.length, "Inconsistent array length");

175:  require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

176:  require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

177:  require(address(_lpToken) != address(0), "Zero address specified");

180:  require(
181:      getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
182:      "Cannot burn LP tokens"
183:  );

184:  require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");

191:  require(result, "Transfer failed");

204:      require(
205:          liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
206:          "Unknown BLS public key"
207:      );

210:      require(
211:          getAccountManager().blsPublicKeyToLifecycleStatus(_blsPubKeys[i]) == IDataStructures.LifecycleStatus.TOKENS_MINTED,
212:          "Derivatives not minted"
213:      );

229:      require(address(token) != address(0), "Invalid BLS key");

230:      require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");

240:  require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

241:  require(_amount <= address(this).balance, "Not enough ETH to withdraw");

242:  require(_wallet != address(0), "Zero address");

245:  require(result, "Transfer failed");

267:      require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");

320:      require(blsPubKey.length > 0, "Invalid token");

346:      require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");

361:  require(_syndicate != address(0), "Invalid configuration");

372:  require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");

373:  require(address(_lpTokenFactory) != address(0), "Zero Address");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

57:    require(_recipient != address(0), "Zero address");

68:            require(success, "Failed to transfer");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

33:    require(
34:        initialOwner != address(0),
35:        "OwnableSmartWallet: Attempting to initialize with zero address owner"
36:    );

75:    require(result, "Failed to execute");

96:    require(
97:        isTransferApproved(owner(), msg.sender),
98:        "OwnableSmartWallet: Transfer is not allowed"
99:     );

111:  require(
112:      to != address(0),
113:      "OwnableSmartWallet: Approval cannot be set for zero address"
114:   );
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

37:    require(owner != address(0), 'Wallet cannot be address 0');
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol

### Using bools for storage variables incurs overhead

Use `uint256(1)` and `uint256(2)` for `true`/`false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

_There are **1** instances of this issue:_

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

120:  bool public enableWhitelisting;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

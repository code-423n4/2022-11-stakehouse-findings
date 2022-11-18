# GAS ISSUES FOR [2022-11-STAKEHOUSE](https://github.com/code-423n4/2022-11-stakehouse)

##  [G-01]  Re-arrange storage variable so they use less contract storage slots and use less gas this way


./contracts/liquid-staking/LiquidStakingManager.sol
```
L90:   currently storage variables are packed in 23 slots but could fit in 22
```

##  [G-02]  Use `++i` instead of `i++`


./contracts/liquid-staking/ETHPoolLPFactory.sol
```
L144:  numberOfLPTokensIssued++;
```

##  [G-03]  `uncheck` the `i++`/`i--` in for loops since there's no way to overflow/underflow


./contracts/liquid-staking/GiantPoolBase.sol
```
L76:   for (uint256 i; i < amountOfTokens; ++i) {
```

./contracts/liquid-staking/StakingFundsVault.sol
```
L152:  for (uint256 i; i < numOfTokens; ++i) {

L166:  for (uint256 i; i < numOfTokens; ++i) {

L203:  for (uint256 i; i < _blsPubKeys.length; ++i) {

L266:  for (uint256 i; i < _blsPublicKeys.length; ++i) {

L276:  for (uint256 i; i < _blsPubKeys.length; ++i) {
```

./contracts/liquid-staking/GiantMevAndFeesPool.sol
```
L38:   for (uint256 i; i < numOfVaults; ++i) {

L64:   for (uint256 i; i < numOfVaults; ++i) {

L90:   for (uint256 i; i < _stakingFundsVaults.length; ++i) {

L117:  for (uint256 i; i < numOfRotations; ++i) {

L135:  for (uint256 i; i < numOfVaults; ++i) {
```

./contracts/liquid-staking/GiantSavETHVaultPool.sol
```
L42:   for (uint256 i; i < numOfSavETHVaults; ++i) {

L78:   for (uint256 i; i < numOfVaults; ++i) {

L128:  for (uint256 i; i < numOfRotations; ++i) {

L146:  for (uint256 i; i < numOfVaults; ++i) {

L148:  for (uint256 j; j < _lpTokens[i].length; ++j) {
```

./contracts/syndicate/Syndicate.sol
```
L211:  for (uint256 i; i < _blsPubKeys.length; ++i) {

L259:  for (uint256 i; i < _blsPubKeys.length; ++i) {

L346:  for (uint256 i; i < _blsPubKeys.length; ++i) {

L420:  for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

L513:  for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

L585:  for (uint256 i; i < _priorityStakers.length; ++i) {

L598:  for (uint256 i; i < _blsPublicKeys.length; ++i) {
```

./contracts/liquid-staking/ETHPoolLPFactory.sol
```
L63:   for (uint256 i; i < numOfRotations; ++i) {
```

./contracts/liquid-staking/LiquidStakingManager.sol
```
L392:  for(uint256 i; i < _blsPubKeys.length; ++i) {

L465:  for(uint256 i; i < len; ++i) {

L538:  for (uint256 i; i < numOfValidators; ++i) {
```

./contracts/liquid-staking/SavETHVault.sol
```
L63:   for (uint256 i; i < numOfValidators; ++i) {

L103:  for (uint256 i; i < numOfTokens; ++i) {

L116:  for (uint256 i; i < numOfTokens; ++i) {
```

##  [G-04]  Skip copying data from calldata to memory for function parameters
Description: Use `calldata` location for reference-type input args

./contracts/syndicate/Syndicate.sol
```
L133:  bytes[] memory _blsPubKeysForSyndicateKnots

L343:  function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {

L359:  function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {

L472:  address[] memory _priorityStakers,

L473:  bytes[] memory _blsPubKeysForSyndicateKnots

L491:  function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {

L583:  function _addPriorityStakers(address[] memory _priorityStakers) internal {

L610:  function _deRegisterKnot(bytes memory _blsPublicKey) internal {

L631:  bytes memory _blsPublicKey
```

./contracts/liquid-staking/StakingFundsVault.sol
```
L355:  function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {

L360:  function _claimFundsFromSyndicateForDistribution(address _syndicate, bytes[] memory _blsPubKeys) internal {
```

./contracts/smart-wallet/OwnableSmartWallet.sol
```
L41:   function execute(address target, bytes memory callData)

L54:   bytes memory callData,

L69:   bytes memory callData,
```

./contracts/liquid-staking/LiquidStakingManager.sol
```
L879:  function _autoStakeWithSyndicate(address _associatedSmartWallet, bytes memory _blsPubKey) internal {
```

##  [G-05]  Spread the `require()` conditions to multiple `require()` statements to save gas from `&&`


./contracts/liquid-staking/LiquidStakingManager.sol
```
L357:  require(_new != address(0) && _current != _new, "New is zero or current");
```

##  [G-06]  `!= 0` comparison is cheaper than `> 0`


./contracts/syndicate/Syndicate.sol
```
L315:  if (unclaimedUserShare > 0) {

L588:  if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();

L656:  if (unclaimedUserShare > 0) {
```

./contracts/liquid-staking/GiantPoolBase.sol
```
L71:   require(amountOfTokens > 0, "Empty arrays");
```

./contracts/liquid-staking/GiantSavETHVaultPool.sol
```
L72:   require(numOfVaults > 0, "Empty arrays");

L123:  require(numOfRotations > 0, "Empty arrays");

L143:  require(numOfVaults > 0, "Empty arrays");
```

./contracts/liquid-staking/StakingFundsVault.sol
```
L71:   require(numOfValidators > 0, "Empty arrays");

L150:  require(numOfTokens > 0, "Empty arrays");

L164:  require(numOfTokens > 0, "Empty arrays");
```

./contracts/liquid-staking/SyndicateRewardsProcessor.sol
```
L37:   if (_balanceOfSender > 0) {

L59:   if (balance > 0) {

L77:   if (_numOfShares > 0) {

L81:   if (unprocessed > 0) {
```

./contracts/liquid-staking/GiantMevAndFeesPool.sol
```
L35:   require(numOfVaults > 0, "Zero vaults");

L112:  require(numOfRotations > 0, "Empty arrays");

L132:  require(numOfVaults > 0, "Empty arrays");
```

./contracts/liquid-staking/SavETHVault.sol
```
L59:   require(numOfValidators > 0, "Empty arrays");

L101:  require(numOfTokens > 0, "Empty arrays");

L114:  require(numOfTokens > 0, "Empty arrays");
```

./contracts/liquid-staking/LiquidStakingManager.sol
```
L532:  require(numOfValidators > 0, "No data");

L583:  require(numOfKnotsToProcess > 0, "Empty array");

L922:  require(_received > 0, "Nothing received");
```

./contracts/liquid-staking/ETHPoolLPFactory.sol
```
L59:   require(numOfRotations > 0, "Empty arrays");
```

##  [G-07]  A < B + 1 is cheaper than A <= B
 

./contracts/liquid-staking/GiantPoolBase.sol
```
L35:   require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");

L53:   require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

L54:   require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");

L94:   require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

L95:   require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");
```

./contracts/liquid-staking/ETHPoolLPFactory.sol
```
L80:   require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

L81:   require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");

L83:   require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

L122:  require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

L130:  require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

./contracts/liquid-staking/StakingFundsVault.sol
```
L176:  require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

L240:  require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

L241:  require(_amount <= address(this).balance, "Not enough ETH to withdraw");
```

./contracts/liquid-staking/GiantSavETHVaultPool.sol
```
L92:   require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");

L127:  require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
```

./contracts/liquid-staking/LiquidStakingManager.sol
```
L256:  require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

L257:  require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

L333:  require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");

L432:  require(len >= 1, "No value provided");

L663:  require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

L936:  require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
```

./contracts/liquid-staking/SavETHVault.sol
```
L127:  require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

L128:  require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

L161:  require(dETHBalance >= 24 ether, "Nothing to withdraw");

L205:  require(address(this).balance >= _amount, "Insufficient withdrawal amount");
```

./contracts/liquid-staking/GiantMevAndFeesPool.sol
```
L116:  require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
```

##  [G-08]  Cache variables that are read multiple times in a single fuction or loop


./contracts/liquid-staking/LiquidStakingManager.sol
```
       /// @audit Cache `stakehouse`. Used 4 times in `_createLSDNStakehouse`
L843:  IERC20 sETH = IERC20(getSlotRegistry().stakeHouseShareTokens(stakehouse));
L847:  stakehouse,
L855:  IStakeHouseRegistry(stakehouse).setGateKeeper(address(gatekeeper));
L875:  emit StakehouseCreated(stakehouseTicker, stakehouse);

       /// @audit Cache `enableWhitelisting`. Used 3 times in `updateWhitelisting`
L268:  require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
L270:  emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);
L272:  return enableWhitelisting;

       /// @audit Cache `smartWalletOfNodeRunner`. Used 3 times in `rotateNodeRunnerOfSmartWallet`
L359:  address wallet = smartWalletOfNodeRunner[_current];
L363:  address newRunnerCurrentWallet = smartWalletOfNodeRunner[_new];
L369:  delete smartWalletOfNodeRunner[_current];
```

./contracts/liquid-staking/ETHPoolLPFactory.sol
```
       /// @audit Cache `KnotAssociatedWithLPToken`. Used 3 times in `rotateLPTokens`
L85:   bytes memory blsPublicKeyOfPreviousKnot = KnotAssociatedWithLPToken[_oldLPToken];
L86:   bytes memory blsPublicKeyOfNewKnot = KnotAssociatedWithLPToken[_newLPToken];
L106:  emit LPTokenMinted(KnotAssociatedWithLPToken[_newLPToken], address(_newLPToken), msg.sender, _amount);
```

##  [G-09]  Use custom errors to save gas


./contracts/liquid-staking/GiantMevAndFeesPool.sol
```
L35:   require(numOfVaults > 0, "Zero vaults");

L36:   require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");

L37:   require(numOfVaults == _amounts.length, "Inconsistent lengths");

L43:   require(
L44:   liquidStakingDerivativeFactory.isLiquidStakingManager(address(sfv.liquidStakingNetworkManager())),
L45:   "Invalid liquid staking manager"
L46:   );

L62:   require(numOfVaults > 0, "Empty array");

L87:   require(_stakingFundsVaults.length == _lpTokens.length, "Inconsistent array lengths");

L112:  require(numOfRotations > 0, "Empty arrays");

L113:  require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");

L114:  require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

L115:  require(numOfRotations == _amounts.length, "Inconsistent arrays");

L116:  require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

L134:  require(numOfVaults == _amounts.length, "Inconsistent arrays");
```

./contracts/smart-wallet/OwnableSmartWallet.sol
```
L75:   require(result, "Failed to execute");

L96:   require(
L97:   isTransferApproved(owner(), msg.sender),
L98:   "OwnableSmartWallet: Transfer is not allowed"
L99:   );

L111:  require(
L112:  to != address(0),
L113:  "OwnableSmartWallet: Approval cannot be set for zero address"
L114:  );
```

./contracts/liquid-staking/ETHPoolLPFactory.sol
```
L60:   require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

L61:   require(numOfRotations == _amounts.length, "Inconsistent arrays");

L77:   require(address(_oldLPToken) != address(0), "Zero address");

L80:   require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

L82:   require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");

L83:   require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

L89:   require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");

L91:   require(
L92:   getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfPreviousKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
L93:   "Lifecycle status must be one"
L94:   );

L96:   require(
L97:   getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
L98:   "Lifecycle status must be one"
L99:   );

L111:  require(_amount >= MIN_STAKING_AMOUNT, "Min amount not reached");

L112:  require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");

L122:  require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

L130:  require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

./contracts/liquid-staking/GiantSavETHVaultPool.sol
```
L36:   require(numOfSavETHVaults > 0, "Empty arrays");

L37:   require(numOfSavETHVaults == _ETHTransactionAmounts.length, "Inconsistent array lengths");

L49:   require(
L50:   liquidStakingDerivativeFactory.isLiquidStakingManager(address(savETHPool.liquidStakingManager())),
L51:   "Invalid liquid staking manager"
L52:   );

L73:   require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

L74:   require(numOfVaults == _amounts.length, "Inconsistent arrays");

L89:   require(vault.isDETHReadyForWithdrawal(address(token)), "dETH is not ready for withdrawal");

L123:  require(numOfRotations > 0, "Empty arrays");

L124:  require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");

L125:  require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

L143:  require(numOfVaults > 0, "Empty arrays");

L144:  require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

L145:  require(numOfVaults == _amounts.length, "Inconsistent arrays");

L149:  require(
L150:  vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
L151:  "ETH is either staked or derivatives minted"
L152:  );
```

./contracts/liquid-staking/LiquidStakingManager.sol
```
L161:  require(msg.sender == dao, "Must be DAO");

L209:  require(smartWallet != address(0), "No wallet found");

L240:  require(_newAddress != address(0), "Zero address");

L250:  require(_commissionPercentage != daoCommissionPercentage, "Same commission percentage");

L257:  require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

L258:  require(numberOfKnots == 0, "Cannot change ticker once house is created");

L268:  require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");

L290:  require(_newRepresentative != address(0), "Zero address");

L291:  require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

L294:  require(smartWallet != address(0), "No smart wallet");

L296:  require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

L309:  require(_newRepresentative != address(0), "Zero address");

L312:  require(smartWallet != address(0), "No smart wallet");

L313:  require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");

L314:  require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

L327:  require(_recipient != address(0), "Zero address");

L328:  require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

L331:  require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");

L333:  require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");

L334:  require(
L335:  getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
L336:  "Initials not registered"
L337:  );

L357:  require(_new != address(0) && _current != _new, "New is zero or current");

L360:  require(wallet != address(0), "Wallet does not exist");

L364:  require(newRunnerCurrentWallet == address(0), "New runner has a wallet");

L393:  require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

L396:  require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

L412:  require(transferResult, "Failed to transfer");

L416:  require(transferResult, "Failed to transfer");

L432:  require(len >= 1, "No value provided");

L433:  require(len == _blsSignatures.length, "Unequal number of array values");

L434:  require(msg.value == len * 4 ether, "Insufficient ether provided");

L435:  require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");

L436:  require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

L461:  require(result, "Transfer failed");

L469:  require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

L532:  require(numOfValidators > 0, "No data");

L534:  require(numOfValidators == _aesEncryptorKeys.length, "Inconsistent array lengths");

L535:  require(numOfValidators == _encryptionSignatures.length, "Inconsistent array lengths");

L544:  require(associatedSmartWallet != address(0), "Unknown BLS public key");

L545:  require(
L546:  getAccountManager().blsPublicKeyToLifecycleStatus(blsPubKey) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
L547:  "Initials not registered"
L548:  );

L583:  require(numOfKnotsToProcess > 0, "Empty array");

L589:  require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

L592:  require(
L593:  getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.DEPOSIT_COMPLETED,
L594:  "Lifecycle status must be two"
L595:  );

L659:  require(_syndicateFactory != address(0), "Zero address");

L685:  require(_nodeRunner != address(0), "Zero address");

L688:  require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

L734:  revert("Unexpected state");

L922:  require(_received > 0, "Nothing received");

L936:  require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

L939:  require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");

L940:  require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

L944:  require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
```

./contracts/liquid-staking/LSDNFactory.sol
```
L51:   require(_liquidStakingManagerImplementation != address(0), "Zero Address");

L53:   require(_lpTokenFactory != address(0), "Zero Address");

L54:   require(_smartWalletFactory != address(0), "Zero Address");

L55:   require(_brand != address(0), "Zero Address");

L56:   require(_savETHVaultDeployer != address(0), "Zero Address");

L58:   require(_optionalGatekeeperDeployer != address(0), "Zero Address");
```

./contracts/liquid-staking/SyndicateRewardsProcessor.sol
```
L57:   require(_recipient != address(0), "Zero address");

L68:   require(success, "Failed to transfer");
```

./contracts/liquid-staking/StakingFundsVault.sol
```
L71:   require(numOfValidators > 0, "Empty arrays");

L79:   require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

L80:   require(
L81:   getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
L82:   "Lifecycle status must be one"
L83:   );

L107:  require(msg.value == totalAmount, "Invalid ETH amount attached");

L114:  require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

L115:  require(
L116:  getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
L117:  "Lifecycle status must be one"
L118:  );

L120:  require(msg.value == _amount, "Must provide correct amount of ETH");

L151:  require(numOfTokens == _amounts.length, "Inconsistent array length");

L165:  require(numOfTokens == _amounts.length, "Inconsistent array length");

L175:  require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

L176:  require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

L177:  require(address(_lpToken) != address(0), "Zero address specified");

L184:  require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");

L191:  require(result, "Transfer failed");

L204:  require(
L205:  liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
L206:  "Unknown BLS public key"
L207:  );

L210:  require(
L211:  getAccountManager().blsPublicKeyToLifecycleStatus(_blsPubKeys[i]) == IDataStructures.LifecycleStatus.TOKENS_MINTED,
L212:  "Derivatives not minted"
L213:  );

L229:  require(address(token) != address(0), "Invalid BLS key");

L230:  require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");

L241:  require(_amount <= address(this).balance, "Not enough ETH to withdraw");

L242:  require(_wallet != address(0), "Zero address");

L245:  require(result, "Transfer failed");

L372:  require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");

L373:  require(address(_lpTokenFactory) != address(0), "Zero Address");
```

./contracts/liquid-staking/LPToken.sol
```
L23:   require(msg.sender == deployer, "Only savETH vault");
```

./contracts/liquid-staking/GiantPoolBase.sol
```
L35:   require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");

L53:   require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

L54:   require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");

L55:   require(idleETH >= _amount, "Come back later or withdraw less ETH");

L72:   require(amountOfTokens == _amounts.length, "Inconsistent array lengths");

L94:   require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

L95:   require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");

L96:   require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");
```

./contracts/smart-wallet/OwnableSmartWalletFactory.sol
```
L37:   require(owner != address(0), 'Wallet cannot be address 0');
```

./contracts/liquid-staking/GiantLP.sol
```
L30:   require(msg.sender == pool, "Only pool");

L35:   require(msg.sender == pool, "Only pool");
```

./contracts/liquid-staking/LPTokenFactory.sol
```
L33:   require(address(_deployer) != address(0), "Zero address");

L34:   require(bytes(_tokenSymbol).length != 0, "Symbol cannot be zero");

L35:   require(bytes(_tokenName).length != 0, "Name cannot be zero");
```

./contracts/liquid-staking/SavETHVault.sol
```
L50:   require(msg.sender == address(liquidStakingManager), "Not the savETH vault manager");

L65:   require(
L66:   getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
L67:   "Lifecycle status must be one"
L68:   );

L76:   require(msg.value == totalAmount, "Invalid ETH amount attached");

L84:   require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

L85:   require(
L86:   getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
L87:   "Lifecycle status must be one"
L88:   );

L90:   require(msg.value == _amount, "Must provide correct amount of ETH");

L101:  require(numOfTokens > 0, "Empty arrays");

L102:  require(numOfTokens == _amounts.length, "Inconsistent array length");

L114:  require(numOfTokens > 0, "Empty arrays");

L115:  require(numOfTokens == _amounts.length, "Inconsisent array length");

L128:  require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

L134:  require(
L135:  validatorStatus == IDataStructures.LifecycleStatus.INITIALS_REGISTERED ||
L136:  validatorStatus == IDataStructures.LifecycleStatus.TOKENS_MINTED,
L137:  "Cannot burn LP tokens"
L138:  );

L161:  require(dETHBalance >= 24 ether, "Nothing to withdraw");

L186:  require(isStaleLiquidity, "Liquidity is still fresh");

L190:  require(result, "Transfer failed");

L205:  require(address(this).balance >= _amount, "Insufficient withdrawal amount");

L207:  require(_smartWallet != address(this), "This address");

L210:  require(result, "Transfer failed");
```

##  [G-10]  Use `A << 1` and `A >> 1` instead of `A * 2` and `A / 2`
Description: saves around 6-8 gas

./contracts/syndicate/Syndicate.sol
```
L546:  return (_ethSinceLastUpdate * PRECISION) / (numberOfRegisteredKnots * 4 ether);
```

./contracts/liquid-staking/LiquidStakingManager.sol
```
L434:  require(msg.value == len * 4 ether, "Insufficient ether provided");
```

##  [G-11]  If a function is not open to any user, it can be marked as `payable` to save gas
Description: Under the hood the compiler checks if a function is payable. The check is skipped if it is marked as such by the developer

./contracts/liquid-staking/LiquidStakingManager.sol
```
L239:  function updateDAOAddress(address _newAddress) external onlyDAO {

L249:  function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {

L255:  function updateTicker(string calldata _newTicker) external onlyDAO {

L278:  function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {

L308:  function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO {
```

./contracts/smart-wallet/OwnableSmartWallet.sol
```
L110:  function setApproval(address to, bool status) external override onlyOwner {
```

./contracts/liquid-staking/StakingFundsVault.sol
```
L56:   function updateDerivativesMinted() external onlyManager {

L239:  function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
```

./contracts/liquid-staking/GiantMevAndFeesPool.sol
```
L146:  function beforeTokenTransfer(address _from, address _to, uint256) external {

L170:  function afterTokenTransfer(address, address _to, uint256) external {
```

./contracts/liquid-staking/GiantLP.sol
```
L29:   function mint(address _recipient, uint256 _amount) external {

L34:   function burn(address _recipient, uint256 _amount) external {
```

./contracts/syndicate/Syndicate.sol
```
L154:  function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {

L161:  function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {

L168:  function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
```

##  [G-12]  Make `constant` and `immutable` variables `private` instead of `public`
Description: Removes the getter function for this variable which saves gas. At the same time the variable get still be read from outside the contract

./contracts/liquid-staking/SyndicateRewardsProcessor.sol
```
L15:   uint256 public constant PRECISION = 1e24;
```

./contracts/syndicate/Syndicate.sol
```
L66:   uint256 public constant PRECISION = 1e24;
```

./contracts/liquid-staking/ETHPoolLPFactory.sol
```
L38:   uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
```

./contracts/liquid-staking/GiantPoolBase.sol
```
L22:   uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
```

##  [G-13]  Consider shortening some string to avoid extra gas


./contracts/liquid-staking/StakingFundsVault.sol
```
L79:   require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

L114:  require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

L120:  require(msg.value == _amount, "Must provide correct amount of ETH");

L240:  require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

L267:  require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
```

./contracts/liquid-staking/SavETHVault.sol
```
L64:   require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

L90:   require(msg.value == _amount, "Must provide correct amount of ETH");

L204:  require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
```

./contracts/liquid-staking/ETHPoolLPFactory.sol
```
L122:  require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

L130:  require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

./contracts/liquid-staking/GiantSavETHVaultPool.sol
```
L149:  require(
L150:  vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
L151:  "ETH is either staked or derivatives minted"
L152:  );
```

./contracts/smart-wallet/OwnableSmartWallet.sol
```
L33:   require(
L34:   initialOwner != address(0),
L35:   "OwnableSmartWallet: Attempting to initialize with zero address owner"
L36:   );

L96:   require(
L97:   isTransferApproved(owner(), msg.sender),
L98:   "OwnableSmartWallet: Transfer is not allowed"
L99:   );

L111:  require(
L112:  to != address(0),
L113:  "OwnableSmartWallet: Approval cannot be set for zero address"
L114:  );
```

./contracts/liquid-staking/LiquidStakingManager.sol
```
L256:  require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

L257:  require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

L268:  require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");

L280:  require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

L291:  require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

L328:  require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

L331:  require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");

L393:  require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

L396:  require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

L437:  require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

L469:  require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

L589:  require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

L662:  require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");

L936:  require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

L940:  require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

L944:  require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
```

./contracts/liquid-staking/GiantPoolBase.sol
```
L55:   require(idleETH >= _amount, "Come back later or withdraw less ETH");
```

##  [G-14]  A=A+B costs less gas than A+=B if A and B are located in storage


./contracts/liquid-staking/SyndicateRewardsProcessor.sol
```
L85:   accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;

L65:   totalClaimed += due;
```

./contracts/liquid-staking/LiquidStakingManager.sol
```
L615:  stakedKnotsOfSmartWallet[smartWallet] -= 1;

L770:  stakedKnotsOfSmartWallet[smartWallet] += 1;

L839:  numberOfKnots += 1;
```

./contracts/syndicate/Syndicate.sol
```
L185:  accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);

L190:  accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;

L225:  totalFreeFloatingShares += _sETHAmount;

L269:  totalFreeFloatingShares -= _sETHAmount;

L621:  totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];

L624:  numberOfRegisteredKnots -= 1;

L226:  sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;

L272:  sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;

L227:  sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;

L511:  accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;

L521:  accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
```

./contracts/liquid-staking/StakingFundsVault.sol
```
L58:   totalShares += 4 ether;
```

./contracts/liquid-staking/GiantPoolBase.sol
```
L39:   idleETH += msg.value;

L57:   idleETH -= _amount;
```


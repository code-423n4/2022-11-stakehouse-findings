# Report

## Gas Optimizations

|         | Issue                                                                                       | 
| ------- | :------------------------------------------------------------------------------------------ | 
| [GAS-1] |Split up require statements instead of && or || |
| [GAS-2] |For loop incrementing can be unsafe|
| [GAS-3] |Use iszero assembly for zero checksx|
| [GAS-4] |Identifying a function as payable saves gas.|
| [GAS-5] |Identifying a constructor as payable saves gas.|
| [GAS-6] |An internal function can save gas vs. a modifier.|
| [GAS-7] |require()/revert() strings longer than 32 bytes cost extra gas |
| [GAS-8] |Multiple mappings of the same type can be combined into a single mapping of an address to a struct, where appropriate |
| [GAS-9] |State variables only set in the constructor should be declared immutable |
| [GAS-10] |Using `private` rather than `public` for constants, saves gas |
| [GAS-11] |The comparison operators >= and <= use more gas than >, <, or ==. |
| [GAS-12] |Using calldata instead of memory for read-only arguments in external functions saves gas |
| [GAS-13] |State variables should be cached in stack variables rather than re-reading them from storage |
| [GAS-14] |If statement cost less if condition is !=0 instead of ==0 or if the first condition is more likely to be met |
| [GAS-15] |Multiplication/division by two should use bit shifting |
| [GAS-16] |`2**<n> - 1 `should be re-written as `type(uint<n>).max`]|
| [GAS-17] |Redundant zero initialization|


### [GAS-1] Split up require statements instead of && or ||

_Instances (2)_:

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  357:         require(_new != address(0) && _current != _new, "New is zero or
  361:         require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");

```

### [GAS-2] For loop incrementing can be unsafe

For loops that use i++ do not need to check for overflow for this operation because the loop would run out of gas long before this point. Making this addition operation unsafe using unchecked saves gas.

Sample code to make the for loop increment unsafe

```solidity
for (uint i = 0; i < length; i = unchecked_inc(i)) {
    // do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
    unchecked {
        return i + 1;
    }
}
```

more details here
https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked

_Instances (36)_:

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol:
  63:         for (uint256 i; i < numOfRotations; ++i) {
```

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol:
   38:         for (uint256 i; i < numOfVaults; ++i) {
   64:         for (uint256 i; i < numOfVaults; ++i) {
   90:         for (uint256 i; i < _stakingFundsVaults.length; ++i) {
  117:         for (uint256 i; i < numOfRotations; ++i) {
  135:         for (uint256 i; i < numOfVaults; ++i) {
  183:         for (uint256 i; i < _lpTokens.length; ++i) {
```

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol:
  76:         for (uint256 i; i < amountOfTokens; ++i) {
```

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol:
   42:         for (uint256 i; i < numOfSavETHVaults; ++i) {
   78:         for (uint256 i; i < numOfVaults; ++i) {
  128:         for (uint256 i; i < numOfRotations; ++i) {
  146:         for (uint256 i; i < numOfVaults; ++i) {
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  392:         for(uint256 i; i < _blsPubKeys.length; ++i) {
  465:         for(uint256 i; i < len; ++i) {
  538:         for (uint256 i; i < numOfValidators; ++i) {
  587:         for (uint256 i; i < numOfKnotsToProcess; ++i) {
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol:
   63:         for (uint256 i; i < numOfValidators; ++i) {
  103:         for (uint256 i; i < numOfTokens; ++i) {
  116:         for (uint256 i; i < numOfTokens; ++i) {
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
   78:         for (uint256 i; i < numOfValidators; ++i) {
  152:         for (uint256 i; i < numOfTokens; ++i) {
  166:         for (uint256 i; i < numOfTokens; ++i) {
  203:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  266:         for (uint256 i; i < _blsPublicKeys.length; ++i) {
  276:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  286:         for (uint256 i; i < _token.length; ++i) {
```

```solidity
File: contracts/syndicate/Syndicate.sol:
  211:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  259:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  301:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  346:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  420:         for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
  513:                     for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
  560:         for (uint256 i; i < knotsToRegister; ++i) {
  585:         for (uint256 i; i < _priorityStakers.length; ++i) {
  598:         for (uint256 i; i < _blsPublicKeys.length; ++i) {
  648:         for (uint256 i; i < _blsPubKeys.length; ++i) {
```

### [GAS-3] Use iszero assembly for zero checks

Comparing a value to zero can be done using the iszero EVM opcode. This can save gas

Source from t11s https://twitter.com/transmissions11/status/1474465495243898885

_Instances (13)_:

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  258:         require(numberOfKnots == 0, "Cannot change ticker once house is created");
  295:         require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
  313:         require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
  598:             if(numberOfKnots == 0) {
  617:             if(stakedKnotsOfSmartWallet[smartWallet] == 0) {
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
  215:             if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {
```

```solidity
File: contracts/syndicate/Syndicate.sol:
  204:         if (_blsPubKeys.length == 0) revert EmptyArray();
  251:         if (_blsPubKeys.length == 0) revert EmptyArray();
  294:         if (_blsPubKeys.length == 0) revert EmptyArray();
  345:         if (numOfKeys == 0) revert EmptyArray();
  557:         if (knotsToRegister == 0) revert EmptyArray();
  584:         if (_priorityStakers.length == 0) revert EmptyArray();
  641:         if (_blsPubKeys.length == 0) revert EmptyArray();
```

### [GAS-4] Identifying a function as payable saves gas.

if not mark as payable compiled code will check that msg.value is zero and thus consume more gas.
Functions that have a modifier like onlyOwner or auth cannot be called by normal users and will not mistakenly receive ETH. These functions can be payable to save gas.

_Instances (19)_:

```solidity
File: contracts/liquid-staking/GiantLP.sol:
  29:         function mint(address _recipient, uint256 _amount) external {
  34:         function burn(address _recipient, uint256 _amount) external {
```

```solidity
File: contracts/liquid-staking/LPToken.sol:
  46:     function mint(address _recipient, uint256 _amount) external onlyDeployer {
  51:     function burn(address _recipient, uint256 _amount) external onlyDeployer {
```

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol:
  114:     function setApproval(address to, bool status) external onlyOwner override {
```

```solidity
File: contracts/syndicate/Syndicate.sol:
  154:     function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {
  161:     function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {
  168:     function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol:
  203:     ) public onlyManager nonReentrant returns (uint256) {
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
   56:     function updateDerivativesMinted() external onlyManager {
  239:     function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  218:     function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {
  230:     ) external onlyDAO {
  239:     function updateDAOAddress(address _newAddress) external onlyDAO {
  249:     function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {
  255:     function updateTicker(string calldata _newTicker) external onlyDAO {
  267:     function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {
  278:     function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {
  308:     function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO {
```

### [GAS-5] Identifying a constructor as payable saves gas.

if not mark as payable compiled code will check that msg.value is zero and thus consume more gas.
Constructors can be payable to save gas. A word of warning though, Constructors should only be called by the admin or deployer and should not mistakenly receive ETH as depending on the contract can see the funds stuck forever.

_Instances (16)_:

```solidity
File: contracts/liquid-staking/GiantLP.sol:
  19:     constructor(
```

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol:
  17:     constructor(LSDNFactory _factory) {
```

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol:
  18:     constructor(LSDNFactory _factory) {
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  166:     constructor() initializer {}
```

```solidity
File: contracts/liquid-staking/LPToken.sol:
  28:     constructor() initializer {}
```

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol:
  18:     constructor(address _lpTokenImplementation) {
```

```solidity
File: contracts/liquid-staking/LSDNFactory.sol:
  41:     constructor(
```

```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol:
  14:     constructor(address _manager) {
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol:
  43:     constructor() initializer {}
```

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol:
  14:     constructor() {
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
  43:     constructor() initializer {}
```

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol:
  14:     constructor() {
```

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol:
  25:     constructor() initializer {}
```

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol:
  22:     constructor() {
```

```solidity
File: contracts/syndicate/Syndicate.sol:
  123:     constructor() initializer {}
```

```solidity
File: contracts/syndicate/SyndicateFactory.sol:
  16:     constructor(address _syndicateImpl) {
```

### [GAS-6] An internal function can save gas vs. a modifier.

A modifier inlines the code of the original function but an internal function does not.

_Instances (4)_:

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  160:     modifier onlyDAO() {
```

```solidity
File: contracts/liquid-staking/LPToken.sol:
  22:     modifier onlyDeployer {
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol:
  49:     modifier onlyManager {
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
  50:     modifier onlyManager() {
```

### [GAS-7] require()/revert() strings longer than 32 bytes cost extra gas

_Instances (23)_:

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol:
  122:             require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
  130:             require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  258:         require(numberOfKnots == 0, "Cannot change ticker once house is created");
  328:         require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
  331:         require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");
  332:         require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
  393:             require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
  396:             require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");
  435:         require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");
  437:         require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
  455:             require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
  469:             require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
  541:             require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
  589:             require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
  936:         require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
  940:         require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
  944:         require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
   79:             require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
  114:         require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
  120:         require(msg.value == _amount, "Must provide correct amount of ETH");
  154:             require(address(token) != address(0), "No ETH staked for specified BLS key");
  240:         require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
  267:             require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
```

### [GAS-8] Multiple mappings of the same type can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot

here we can reduce the mappings for `nodeRunner` and `smart wallet` addresses
_Instances (18)_:

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  123:     mapping(address => bool) public isNodeRunnerWhitelisted;
  126:     mapping(address => address) public smartWalletRepresentative;
  129:     mapping(bytes => address) public smartWalletOfKnot;
  132:     mapping(address => address) public smartWalletOfNodeRunner;
  135:     mapping(address => address) public nodeRunnerOfSmartWallet;
  138:     mapping(address => uint256) public stakedKnotsOfSmartWallet;
  141:     mapping(address => address) public smartWalletDormantRepresentative;
  149:     mapping(address => bool) public bannedNodeRunners;
```

here we can reduce the mappings for `knot`

```solidity
File: contracts/syndicate/Syndicate.sol:
   90:     mapping(bytes => bool) public isKnotRegistered;
   96:     mapping(address => bool) public isPriorityStaker;
   99:     mapping(bytes => uint256) public sETHTotalStakeForKnot;
  102:     mapping(bytes => mapping(address => uint256)) public sETHStakedBalanceForKnot;
  105:     mapping(bytes => mapping(address => uint256)) public sETHUserClaimForKnot;
  108:     mapping(bytes => uint256) public totalETHProcessedPerCollateralizedKnot;
  111:     mapping(bytes => mapping(address => uint256)) public accruedEarningPerCollateralizedSlotOwnerOfKnot;
  114:     mapping(bytes => mapping(address => uint256)) public claimedPerCollateralizedSlotOwnerOfKnot;
  117:     mapping(bytes => bool) public isNoLongerPartOfSyndicate;
  120:     mapping(bytes => uint256) public lastAccumulatedETHPerFreeFloatingShare;
```

### [GAS-9] State variables only set in the constructor should be declared immutable

_Instances (7)_:

```solidity
File: contracts/liquid-staking/GiantLP.sol:
  20:     address _pool,
  21:     address _transferHookProcessor,
```

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol:
  17:     constructor(LSDNFactory _factory) {
```

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol:
  18:     constructor(address _lpTokenImplementation) {
```

```solidity
File: contracts/liquid-staking/LSDNFactory.sol:
  41:     constructor(
```

```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol:
  14:     constructor(address _manager) {
```

```solidity
File: contracts/syndicate/SyndicateFactory.sol:
  16:     constructor(address _syndicateImpl) {
```

### [GAS-10] Using `private` rather than `public` for constants, saves gas

It avoids creating a getter for the constant.
_Instances (4)_:

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol:
  38:     uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
```

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol:
  22:     uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
```

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol:
  15:     uint256 public constant PRECISION = 1e24;
```

```solidity
File: contracts/syndicate/Syndicate.sol:
  66:     uint256 public constant PRECISION = 1e24;
```

### [GAS-11] The comparison operators >= and <= use more gas than >, <, or ==.

In the EVM, there is no opcode for >= or <=. When using greater than or equal, two operations are performed: > and =.
Using strict comparison operators hence saves gas.

_Instances (32)_:

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol:
   81:         require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
   83:         require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");
  122:             require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
  130:             require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  257:         require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
  663:         require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
  949:         require(_commissionPercentage <= MODULO, "Invalid commission");
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol:
  128:         require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
  176:         require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");
  241:         require(_amount <= address(this).balance, "Not enough ETH to withdraw");

  contracts/liquid-staking/ETHPoolLPFactory.sol:
   80:         require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
  111:         require(_amount >= MIN_STAKING_AMOUNT, "Min amount not reached");
```

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol:
  116:         require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
```

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol:
  35:         require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");
  53:         require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
  54:         require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");
  55:         require(idleETH >= _amount, "Come back later or withdraw less ETH");
  94:         require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
  95:         require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");
```

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol:
   92:                 require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");
  127:         require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  256:         require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
  333:         require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");
  432:         require(len >= 1, "No value provided");
  662:         require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
  936:         require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol:
  127:         require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
  161:                 require(dETHBalance >= 24 ether, "Nothing to withdraw");
  204:         require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
  205:         require(address(this).balance >= _amount, "Insufficient withdrawal amount");
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
  175:         require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
  240:         require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
```

### [GAS-12] Using calldata instead of memory for read-only arguments in external functions saves gas

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
  355:     function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {
```

```solidity
File: contracts/syndicate/Syndicate.sol:
  337:     function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {
  343:     function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {
```

### [GAS-13] State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching will replace each Gwarmaccess (100 gas) with a much cheaper stack read.
Avoid accessing a storage variable inside a loop i.e `idleETH -= ..`. Cache the result of function that is called several times and for which the output will be the same throughout the function i.e. `getDETH()`

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol:
  40:          idleETH -= _ETHTransactionAmounts[i];
  154:         address(lpTokenETH),
```

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol:
  46:          idleETH -= _ETHTransactionAmounts[i];
  77:          uint256 dETHReceivedFromAllSavETHVaults = getDETH().balanceOf(address(this)); // getDETH() called several times
```

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol:
  243:      emit UpdateDAOAddress(dao, _newAddress);
  270:      emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);
  282:      isNodeRunnerWhitelisted[_nodeRunner] = isWhitelisted;
  299:      _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);
  317:      _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);
```

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol:
   92:             getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfPreviousKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED, // getAccountManager() called several times
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  961:         address(getTransactionRouter()), // getTransactionRouter() called several times
```

```solidity
File: contracts/liquid-staking/savETHVault.sol:
  159:  uint256 savETHBalance = getSavETHRegistry().dETHToSavETH(dETHBalance); // getSavETHRegistry() called several times
  179:  uint256 dETHRedeemed = getSavETHRegistry().savETHToDETH(redemptionValue); // getSavETHRegistry() called several times
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol:
  219:     liquidStakingNetworkManager.syndicate(), // syndicate() called several times
```

### [GAS-14] If statement cost less if condition is !=0 instead of ==0 or if the first condition is more likely to be met

You can save gas by switch the statement and inverting the condition. You can save even more gas if the negated condition is more likely to happen.
i.e you can replace

```
if (x == 0) {
    do A
} else {
    do B
}
```

by

```
if (x != 0) {
    do B
} else {
    do A
}
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol:
  819:             if (numberOfKnots == 0) {
```

```solidity
File: contracts/syndicate/Syndicate.sol:
607: if (numberOfCollateralisedSlotOwnersForKnot == 1) {
```
### [GAS-15] Multiplication/division by two should use bit shifting

`<x> * 2` is equivalent to `<x> << 1 `and `<x> / 2 `is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

```solidity
contracts/syndicate/Syndicate.sol:
  435:         return ethPerKnot / 2;
```
### [GAS-16] `2**<n> - 1 `should be re-written as `type(uint<n>).max`

Earlier versions of solidity can use uint<n>(-1) instead. Expressions not including the - 1 can often be re-written to accommodate the change (e.g. by using a > rather than a >=, which will also save some gas)

```solidity
contracts/liquid-staking/LiquidStakingManager.sol:
  1118:         sETH.approve(syndicate, (2**256) - 1);
```
 

### [GAS-17] Redundant zero initialization

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas.

_Instances (2)_:

```solidity
File: testing/StakeHouseRegistry.sol

54:          knotMemberIndex = 0;

66:          knotMemberIndex = 0;

```
 

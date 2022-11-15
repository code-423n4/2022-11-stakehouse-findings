## [G-01] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::90 => for (uint256 i; i < _stakingFundsVaults.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::183 => for (uint256 i; i < _lpTokens.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::82 => for (uint256 j; j < _lpTokens[i].length; ++j) {
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::148 => for (uint256 j; j < _lpTokens[i].length; ++j) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::392 => for(uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::203 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::266 => for (uint256 i; i < _blsPublicKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::276 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::286 => for (uint256 i; i < _token.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::211 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::259 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::301 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::346 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::585 => for (uint256 i; i < _priorityStakers.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::598 => for (uint256 i; i < _blsPublicKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::648 => for (uint256 i; i < _blsPubKeys.length; ++i) {
```

## [G-02] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::59 => require(numOfRotations > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::35 => require(numOfVaults > 0, "Zero vaults");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::62 => require(numOfVaults > 0, "Empty array");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::112 => require(numOfRotations > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::132 => require(numOfVaults > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::71 => require(amountOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::36 => require(numOfSavETHVaults > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::72 => require(numOfVaults > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::123 => require(numOfRotations > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::143 => require(numOfVaults > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::386 => require(_blsPubKeys.length > 0, "No BLS keys specified");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::532 => require(numOfValidators > 0, "No data");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::583 => require(numOfKnotsToProcess > 0, "Empty array");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::922 => require(_received > 0, "Nothing received");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::59 => require(numOfValidators > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::101 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::114 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::71 => require(numOfValidators > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::150 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::164 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::320 => require(blsPubKey.length > 0, "Invalid token");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::346 => require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");
```

## [G-03] Long Revert Strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::122 => require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::130 => require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::55 => require(idleETH >= _amount, "Come back later or withdraw less ETH");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::256 => require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::257 => require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::258 => require(numberOfKnots == 0, "Cannot change ticker once house is created");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::268 => require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::280 => require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::291 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::328 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::331 => require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::332 => require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::393 => require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::396 => require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::435 => require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::437 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::455 => require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::469 => require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::541 => require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::589 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::662 => require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::663 => require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::936 => require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::939 => require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::940 => require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::944 => require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::64 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::84 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::90 => require(msg.value == _amount, "Must provide correct amount of ETH");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::204 => require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::79 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::114 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::120 => require(msg.value == _amount, "Must provide correct amount of ETH");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::154 => require(address(token) != address(0), "No ETH staked for specified BLS key");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::240 => require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::267 => require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
```

## [G-04] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::434 => require(msg.value == len * 4 ether, "Insufficient ether provided");
```

## [G-05] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::359 => function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {
```

## [G-06] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::14 => address public immutable masterWallet;
```

## [G-07] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::46 => function mint(address _recipient, uint256 _amount) external onlyDeployer {
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::51 => function burn(address _recipient, uint256 _amount) external onlyDeployer {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::218 => function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::239 => function updateDAOAddress(address _newAddress) external onlyDAO {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::249 => function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::255 => function updateTicker(string calldata _newTicker) external onlyDAO {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::267 => function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::278 => function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::308 => function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::56 => function updateDerivativesMinted() external onlyManager {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::239 => function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::114 => function setApproval(address to, bool status) external onlyOwner override {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::154 => function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::161 => function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::168 => function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
```

## [G-08] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::100 => function _onDepositETH() internal virtual {}
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::103 => function _onWithdraw(LPToken[] calldata _lpTokens) internal virtual {}
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::28 => constructor() initializer {}
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::166 => constructor() initializer {}
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::629 => receive() external payable {}
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::43 => constructor() initializer {}
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::43 => constructor() initializer {}
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::98 => receive() external payable {}
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::25 => constructor() initializer {}
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::123 => constructor() initializer {}
```

## [G-09] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::39 => mapping(address => bool) public isLiquidStakingManager;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::120 => bool public enableWhitelisting;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::123 => mapping(address => bool) public isNodeRunnerWhitelisted;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::149 => mapping(address => bool) public bannedNodeRunners;
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::20 => mapping(address => bool) public walletExists;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::90 => mapping(bytes => bool) public isKnotRegistered;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::96 => mapping(address => bool) public isPriorityStaker;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::117 => mapping(bytes => bool) public isNoLongerPartOfSyndicate;
```

## [G-10] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::63 => for (uint256 i; i < numOfRotations; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::38 => for (uint256 i; i < numOfVaults; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::64 => for (uint256 i; i < numOfVaults; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::90 => for (uint256 i; i < _stakingFundsVaults.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::117 => for (uint256 i; i < numOfRotations; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::135 => for (uint256 i; i < numOfVaults; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::183 => for (uint256 i; i < _lpTokens.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::76 => for (uint256 i; i < amountOfTokens; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::42 => for (uint256 i; i < numOfSavETHVaults; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::78 => for (uint256 i; i < numOfVaults; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::82 => for (uint256 j; j < _lpTokens[i].length; ++j) {
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::128 => for (uint256 i; i < numOfRotations; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::146 => for (uint256 i; i < numOfVaults; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::148 => for (uint256 j; j < _lpTokens[i].length; ++j) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::392 => for(uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::465 => for(uint256 i; i < len; ++i) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::538 => for (uint256 i; i < numOfValidators; ++i) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::587 => for (uint256 i; i < numOfKnotsToProcess; ++i) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::63 => for (uint256 i; i < numOfValidators; ++i) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::103 => for (uint256 i; i < numOfTokens; ++i) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::116 => for (uint256 i; i < numOfTokens; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::78 => for (uint256 i; i < numOfValidators; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::152 => for (uint256 i; i < numOfTokens; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::166 => for (uint256 i; i < numOfTokens; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::203 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::266 => for (uint256 i; i < _blsPublicKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::276 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::286 => for (uint256 i; i < _token.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::211 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::259 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::301 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::346 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::420 => for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::513 => for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::560 => for (uint256 i; i < knotsToRegister; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::585 => for (uint256 i; i < _priorityStakers.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::598 => for (uint256 i; i < _blsPublicKeys.length; ++i) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::648 => for (uint256 i; i < _blsPubKeys.length; ++i) {
```

## [G-11] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::40 => idleETH -= _ETHTransactionAmounts[i];
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::39 => idleETH += msg.value;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::57 => idleETH -= _amount;
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::46 => idleETH -= transactionAmount;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::615 => stakedKnotsOfSmartWallet[smartWallet] -= 1;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::770 => stakedKnotsOfSmartWallet[smartWallet] += 1;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::782 => numberOfKnots += 1;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::839 => numberOfKnots += 1;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::71 => totalAmount += amount;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::58 => totalShares += 4 ether;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::97 => totalAmount += amount;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::278 => totalAccumulated += previewAccumulatedETH(_user, token);
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::287 => totalAccumulated += previewAccumulatedETH(_user, _token[i]);
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::65 => totalClaimed += due;
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::85 => accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::185 => accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::190 => accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::225 => totalFreeFloatingShares += _sETHAmount;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::226 => sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::227 => sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::269 => totalFreeFloatingShares -= _sETHAmount;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::272 => sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::273 => sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::317 => totalClaimed += unclaimedUserShare;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::430 => currentAccrued +=
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::511 => accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::521 => accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::558 => numberOfRegisteredKnots += knotsToRegister;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::621 => totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::624 => numberOfRegisteredKnots -= 1;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::658 => totalClaimed += unclaimedUserShare;
```

## [G-12] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::63 => return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));
```

## [G-13] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::59 => require(numOfRotations > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::60 => require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::61 => require(numOfRotations == _amounts.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::77 => require(address(_oldLPToken) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::78 => require(address(_newLPToken) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::79 => require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::80 => require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::81 => require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::82 => require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::83 => require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::88 => require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::89 => require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::111 => require(_amount >= MIN_STAKING_AMOUNT, "Min amount not reached");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::112 => require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::122 => require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::130 => require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::30 => require(msg.sender == pool, "Only pool");
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::35 => require(msg.sender == pool, "Only pool");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::35 => require(numOfVaults > 0, "Zero vaults");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::36 => require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::37 => require(numOfVaults == _amounts.length, "Inconsistent lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::62 => require(numOfVaults > 0, "Empty array");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::63 => require(numOfVaults == _blsPublicKeysForKnots.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::87 => require(_stakingFundsVaults.length == _lpTokens.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::112 => require(numOfRotations > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::113 => require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::114 => require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::115 => require(numOfRotations == _amounts.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::116 => require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::132 => require(numOfVaults > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::133 => require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::134 => require(numOfVaults == _amounts.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::147 => require(msg.sender == address(lpTokenETH), "Caller is not giant LP");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::171 => require(msg.sender == address(lpTokenETH), "Caller is not giant LP");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::35 => require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::36 => require(msg.value == _amount, "Value equal to amount");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::53 => require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::54 => require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::55 => require(idleETH >= _amount, "Come back later or withdraw less ETH");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::61 => require(success, "Failed to transfer ETH");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::71 => require(amountOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::72 => require(amountOfTokens == _amounts.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::94 => require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::95 => require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::96 => require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::36 => require(numOfSavETHVaults > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::37 => require(numOfSavETHVaults == _ETHTransactionAmounts.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::38 => require(numOfSavETHVaults == _blsPublicKeys.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::39 => require(numOfSavETHVaults == _stakeAmounts.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::72 => require(numOfVaults > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::73 => require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::74 => require(numOfVaults == _amounts.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::89 => require(vault.isDETHReadyForWithdrawal(address(token)), "dETH is not ready for withdrawal");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::92 => require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::123 => require(numOfRotations > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::124 => require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::125 => require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::126 => require(numOfRotations == _amounts.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::127 => require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::143 => require(numOfVaults > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::144 => require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::145 => require(numOfVaults == _amounts.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::23 => require(msg.sender == deployer, "Only savETH vault");
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::19 => require(_lpTokenImplementation != address(0), "Address cannot be zero");
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::33 => require(address(_deployer) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::34 => require(bytes(_tokenSymbol).length != 0, "Symbol cannot be zero");
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::35 => require(bytes(_tokenName).length != 0, "Name cannot be zero");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::51 => require(_liquidStakingManagerImplementation != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::52 => require(_syndicateFactory != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::53 => require(_lpTokenFactory != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::54 => require(_smartWalletFactory != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::55 => require(_brand != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::56 => require(_savETHVaultDeployer != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::57 => require(_stakingFundsVaultDeployer != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::58 => require(_optionalGatekeeperDeployer != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::161 => require(msg.sender == dao, "Must be DAO");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::209 => require(smartWallet != address(0), "No wallet found");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::240 => require(_newAddress != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::241 => require(_newAddress != dao, "Same address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::250 => require(_commissionPercentage != daoCommissionPercentage, "Same commission percentage");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::256 => require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::257 => require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::258 => require(numberOfKnots == 0, "Cannot change ticker once house is created");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::268 => require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::279 => require(_nodeRunner != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::280 => require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::290 => require(_newRepresentative != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::291 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::294 => require(smartWallet != address(0), "No smart wallet");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::295 => require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::296 => require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::309 => require(_newRepresentative != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::312 => require(smartWallet != address(0), "No smart wallet");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::313 => require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::314 => require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::327 => require(_recipient != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::328 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::331 => require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::332 => require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::333 => require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::357 => require(_new != address(0) && _current != _new, "New is zero or current");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::360 => require(wallet != address(0), "Wallet does not exist");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::361 => require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::364 => require(newRunnerCurrentWallet == address(0), "New runner has a wallet");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::386 => require(_blsPubKeys.length > 0, "No BLS keys specified");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::387 => require(_recipient != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::390 => require(smartWallet != address(0), "Unknown node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::393 => require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::396 => require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::412 => require(transferResult, "Failed to transfer");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::416 => require(transferResult, "Failed to transfer");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::432 => require(len >= 1, "No value provided");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::433 => require(len == _blsSignatures.length, "Unequal number of array values");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::434 => require(msg.value == len * 4 ether, "Insufficient ether provided");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::435 => require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::436 => require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::437 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::455 => require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::461 => require(result, "Transfer failed");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::469 => require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::532 => require(numOfValidators > 0, "No data");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::533 => require(numOfValidators == _ciphertexts.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::534 => require(numOfValidators == _aesEncryptorKeys.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::535 => require(numOfValidators == _encryptionSignatures.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::536 => require(numOfValidators == _dataRoots.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::541 => require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::544 => require(associatedSmartWallet != address(0), "Unknown BLS public key");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::583 => require(numOfKnotsToProcess > 0, "Empty array");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::584 => require(numOfKnotsToProcess == _beaconChainBalanceReports.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::585 => require(numOfKnotsToProcess == _reportSignatures.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::589 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::658 => require(_dao != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::659 => require(_syndicateFactory != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::660 => require(_smartWalletFactory != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::661 => require(_brand != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::662 => require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::663 => require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::685 => require(_nodeRunner != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::688 => require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::922 => require(_received > 0, "Nothing received");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::936 => require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::939 => require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::940 => require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::943 => require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::944 => require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::949 => require(_commissionPercentage <= MODULO, "Invalid commission");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::50 => require(msg.sender == address(liquidStakingManager), "Not the savETH vault manager");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::59 => require(numOfValidators > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::60 => require(numOfValidators == _amounts.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::64 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::76 => require(msg.value == totalAmount, "Invalid ETH amount attached");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::84 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::90 => require(msg.value == _amount, "Must provide correct amount of ETH");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::101 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::102 => require(numOfTokens == _amounts.length, "Inconsistent array length");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::114 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::115 => require(numOfTokens == _amounts.length, "Inconsisent array length");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::127 => require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::128 => require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::161 => require(dETHBalance >= 24 ether, "Nothing to withdraw");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::186 => require(isStaleLiquidity, "Liquidity is still fresh");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::190 => require(result, "Transfer failed");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::204 => require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::205 => require(address(this).balance >= _amount, "Insufficient withdrawal amount");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::206 => require(_smartWallet != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::207 => require(_smartWallet != address(this), "This address");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::210 => require(result, "Transfer failed");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::236 => require(_liquidStakingManagerAddress != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::237 => require(address(_lpTokenFactory) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::51 => require(msg.sender == address(liquidStakingNetworkManager), "Only network manager");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::71 => require(numOfValidators > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::72 => require(numOfValidators == _amounts.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::79 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::107 => require(msg.value == totalAmount, "Invalid ETH amount attached");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::114 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::120 => require(msg.value == _amount, "Must provide correct amount of ETH");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::150 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::151 => require(numOfTokens == _amounts.length, "Inconsistent array length");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::154 => require(address(token) != address(0), "No ETH staked for specified BLS key");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::164 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::165 => require(numOfTokens == _amounts.length, "Inconsistent array length");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::175 => require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::176 => require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::177 => require(address(_lpToken) != address(0), "Zero address specified");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::184 => require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::191 => require(result, "Transfer failed");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::229 => require(address(token) != address(0), "Invalid BLS key");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::230 => require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::240 => require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::241 => require(_amount <= address(this).balance, "Not enough ETH to withdraw");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::242 => require(_wallet != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::245 => require(result, "Transfer failed");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::267 => require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::320 => require(blsPubKey.length > 0, "Invalid token");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::346 => require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::361 => require(_syndicate != address(0), "Invalid configuration");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::372 => require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::373 => require(address(_lpTokenFactory) != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::57 => require(_recipient != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::68 => require(success, "Failed to transfer");
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::79 => require(result, "Failed to execute");
```

## [G-14] Don't compare boolean expressions to boolean literals

The extran comparison wastes gas
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::436 => require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::688 => require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::612 => if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
```

## [G-15] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::357 => require(_new != address(0) && _current != _new, "New is zero or current");
```

## [G-16] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::176 => function totalRewardsReceived() public view override returns (uint256) {
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::34 => function depositETH(uint256 _amount) public payable {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::514 => function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::239 => function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::458 => function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {
```

## [G-17] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::921 => function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {
```

## [G-18] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::123 => mapping(address => bool) public isNodeRunnerWhitelisted;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::126 => mapping(address => address) public smartWalletRepresentative;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::132 => mapping(address => address) public smartWalletOfNodeRunner;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::135 => mapping(address => address) public nodeRunnerOfSmartWallet;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::138 => mapping(address => uint256) public stakedKnotsOfSmartWallet;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::141 => mapping(address => address) public smartWalletDormantRepresentative;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::149 => mapping(address => bool) public bannedNodeRunners;
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::27 => mapping(address => mapping(address => uint256)) public claimed;
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::22 => mapping(address => mapping(address => bool)) internal _isTransferApproved;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::96 => mapping(address => bool) public isPriorityStaker;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::102 => mapping(bytes => mapping(address => uint256)) public sETHStakedBalanceForKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::105 => mapping(bytes => mapping(address => uint256)) public sETHUserClaimForKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::111 => mapping(bytes => mapping(address => uint256)) public accruedEarningPerCollateralizedSlotOwnerOfKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::114 => mapping(bytes => mapping(address => uint256)) public claimedPerCollateralizedSlotOwnerOfKnot;
```

## [G-19] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::77 => require(address(_oldLPToken) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::78 => require(address(_newLPToken) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::117 => if(address(lpToken) != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::40 => if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).beforeTokenTransfer(_from, _to, _amount);
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::46 => if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).afterTokenTransfer(_from, _to, _amount);
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::151 => if (_from != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::62 => if (address(transferHookProcessor) != address(0)) transferHookProcessor.beforeTokenTransfer(_from, _to, _amount);
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::69 => if (address(transferHookProcessor) != address(0)) transferHookProcessor.afterTokenTransfer(_from, _to, _amount);
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::19 => require(_lpTokenImplementation != address(0), "Address cannot be zero");
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::33 => require(address(_deployer) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::51 => require(_liquidStakingManagerImplementation != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::52 => require(_syndicateFactory != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::53 => require(_lpTokenFactory != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::54 => require(_smartWalletFactory != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::55 => require(_brand != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::56 => require(_savETHVaultDeployer != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::57 => require(_stakingFundsVaultDeployer != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::58 => require(_optionalGatekeeperDeployer != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::209 => require(smartWallet != address(0), "No wallet found");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::240 => require(_newAddress != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::279 => require(_nodeRunner != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::290 => require(_newRepresentative != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::294 => require(smartWallet != address(0), "No smart wallet");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::309 => require(_newRepresentative != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::312 => require(smartWallet != address(0), "No smart wallet");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::327 => require(_recipient != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::357 => require(_new != address(0) && _current != _new, "New is zero or current");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::360 => require(wallet != address(0), "Wallet does not exist");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::387 => require(_recipient != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::390 => require(smartWallet != address(0), "Unknown node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::454 => if(smartWalletRepresentative[smartWallet] != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::496 => return smartWalletOfKnot[_blsPublicKeyOfKnot] != address(0);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::501 => return !isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot) || bannedBLSPublicKeys[_blsPublicKeyOfKnot] != address(0);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::544 => require(associatedSmartWallet != address(0), "Unknown BLS public key");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::563 => if(representative != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::658 => require(_dao != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::659 => require(_syndicateFactory != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::660 => require(_smartWalletFactory != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::661 => require(_brand != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::685 => require(_nodeRunner != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::700 => if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::854 => if (address(gatekeeper) != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::939 => require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::943 => require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::206 => require(_smartWallet != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::236 => require(_liquidStakingManagerAddress != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::237 => require(address(_lpTokenFactory) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::86 => if (address(tokenForKnot) != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::127 => if (address(tokenForKnot) != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::154 => require(address(token) != address(0), "No ETH staked for specified BLS key");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::177 => require(address(_lpToken) != address(0), "Zero address specified");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::229 => require(address(token) != address(0), "Invalid BLS key");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::242 => require(_wallet != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::317 => if (syndicate != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::332 => if (_from != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::344 => if (LiquidStakingManager(payable(liquidStakingNetworkManager)).syndicate() != address(0)) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::361 => require(_syndicate != address(0), "Invalid configuration");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::372 => require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::373 => require(address(_lpTokenFactory) != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::57 => require(_recipient != address(0), "Zero address");
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::34 => initialOwner != address(0),
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::116 => to != address(0),
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::37 => require(owner != address(0), 'Wallet cannot be address 0');
```

## [G-20] Use selfbalance()

Use selfbalance() instead of address(this).balance when getting your contract's balance of ETH to save gas.

```
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::177 => return address(this).balance + totalClaimed - idleETH;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::400 => uint256 balBefore = address(this).balance;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::410 => (uint256 nodeRunnerAmount, uint256 daoAmount) = _calculateCommission(address(this).balance - balBefore);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::205 => require(address(this).balance >= _amount, "Insufficient withdrawal amount");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::241 => require(_amount <= address(this).balance, "Not enough ETH to withdraw");
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::94 => return address(this).balance + totalClaimed;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::465 => return address(this).balance + totalClaimed;
```

## [G-22] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::110 => function _depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount, bool _enableTransferHook) internal {
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::93 => function _assertUserHasEnoughGiantLPToClaimVaultLP(LPToken _token, uint256 _amount) internal view {
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::100 => function _onDepositETH() internal virtual {}
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::103 => function _onWithdraw(LPToken[] calldata _lpTokens) internal virtual {}
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::684 => function _isNodeRunnerValid(address _nodeRunner) internal view returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::904 => function _initSavETHVault(address _savETHVaultDeployer, address _lpTokenFactory) internal virtual {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::911 => function _initStakingFundsVault(address _stakingFundsVaultDeployer, address _tokenFactory) internal virtual {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::921 => function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::934 => function _assertEtherIsReadyForValidatorStaking(bytes calldata blsPubKey) internal view {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::235 => function _init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) internal {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::371 => function _init(LiquidStakingManager _liquidStakingNetworkManager, LPTokenFactory _lpTokenFactory) internal virtual {
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::76 => function _updateAccumulatedETHPerLP(uint256 _numOfShares) internal {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::538 => function _calculateCollateralizedETHOwedPerKnot() internal view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::545 => function _calculateNewAccumulatedETHPerCollateralizedShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::597 => function _deRegisterKnots(bytes[] calldata _blsPublicKeys) internal {
```

## [G-24] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::110 => function _depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount, bool _enableTransferHook) internal {
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::76 => function _updateAccumulatedETHPerLP(uint256 _numOfShares) internal {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::538 => function _calculateCollateralizedETHOwedPerKnot() internal view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::545 => function _calculateNewAccumulatedETHPerCollateralizedShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) {
```
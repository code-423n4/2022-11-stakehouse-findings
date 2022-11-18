## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are the contract instances missing NatSpec in its entirety:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateErrors.sol

Here are the function instances missing full or partial set of `@param`s:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L19

```
    function isMemberPermitted(bytes calldata _blsPublicKeyOfKnot) external override view returns (bool) {
```
[File: OwnableSmartWalletFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol)
```
32:    function createWallet(address owner) external returns (address wallet) {

36:    function _createWallet(address owner) internal returns (address wallet) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L27

```
    function deployLPToken(
```
[File: GiantLP.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol)

```
29:    function mint(address _recipient, uint256 _amount) external {

34:    function burn(address _recipient, uint256 _amount) external {

39:    function _beforeTokenTransfer(address _from, address _to, uint256 _amount) internal override {

43:    function _afterTokenTransfer(address _from, address _to, uint256 _amount) internal override {
```
[File: LPToken.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol)

```
32:    function init(

46:    function mint(address _recipient, uint256 _amount) external onlyDeployer {

51:    function burn(address _recipient, uint256 _amount) external onlyDeployer {

56:    function liquidStakingManager() external view returns (address) {

61:    function _beforeTokenTransfer(address _from, address _to, uint256 _amount) internal override {

66:    function _afterTokenTransfer(address _from, address _to, uint256 _amount) internal override {
```
[File: SyndicateFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol)

```
21:    function deploySyndicate(

48:    function calculateSyndicateDeploymentAddress(

58:    function calculateDeploymentSalt(
```
[File: GiantPoolBase.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol)

```
34:    function depositETH(uint256 _amount) public payable {

93:    function _assertUserHasEnoughGiantLPToClaimVaultLP(LPToken _token, uint256 _amount) internal view {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L73

```
    function deployNewLiquidStakingDerivativeNetwork(
```
[File: OwnableSmartWallet.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol)

```
28:    function initialize(address initialOwner)

41:    function execute(address target, bytes memory callData)

52:    function execute(

67:    function rawExecute(

94:    function transferOwnership(address newOwner)

114:    function setApproval(address to, bool status) external onlyOwner override {

139:    function isTransferApproved(address from, address to)
```
[Line: SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)

```
218:    function isBLSPublicKeyPartOfLSDNetwork(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) {

223:    function isBLSPublicKeyBanned(bytes calldata _blsPublicKeyOfKnot) public view returns (bool) {

228:    function isDETHReadyForWithdrawal(address _lpTokenAddress) external view returns (bool) {

235:    function _init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) internal {
```
[File: GiantMevAndFeesPool.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol)

```
56:    function claimRewards(

82:    function previewAccumulatedETH(

146:    function beforeTokenTransfer(address _from, address _to, uint256) external {

170:    function afterTokenTransfer(address, address _to, uint256) external {

181:    function _onWithdraw(LPToken[] calldata _lpTokens) internal override {
```
[File: StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol)

```
199:    function claimRewards(

274:    function batchPreviewAccumulatedETHByBLSKeys(address _user, bytes[] calldata _blsPubKeys) external view returns (uint256) {

284:    function batchPreviewAccumulatedETH(address _user, LPToken[] calldata _token) external view returns (uint256) {

293:    function previewAccumulatedETH(address _user, LPToken _token) public view returns (uint256) {

315:    function beforeTokenTransfer(address _from, address _to, uint256) external override {

343:    function afterTokenTransfer(address, address _to, uint256) external override {

360:    function _claimFundsFromSyndicateForDistribution(address _syndicate, bytes[] memory _blsPubKeys) internal {

371:    function _init(LiquidStakingManager _liquidStakingNetworkManager, LPTokenFactory _lpTokenFactory) internal virtual {
```
[File: Syndicate.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol)

```
154:    function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {

290:     function claimAsCollateralizedSLOTOwner(

469:    function _initialize(

491:    function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {

545:    function _calculateNewAccumulatedETHPerCollateralizedShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) {

550:    function _calculateNewAccumulatedETHPerFreeFloatingShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) {

555:    function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots) internal {

583:    function _addPriorityStakers(address[] memory _priorityStakers) internal {

597:    function _deRegisterKnots(bytes[] calldata _blsPublicKeys) internal {

610:    function _deRegisterKnot(bytes memory _blsPublicKey) internal {

630:    function _getCorrectAccumulatedETHPerFreeFloatingShareForBLSPublicKey(

640:    function _claimAsStaker(address _recipient, bytes[] calldata _blsPubKeys) internal {
```
[File: LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol)

```
218:    function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {

239:    function updateDAOAddress(address _newAddress) external onlyDAO {

249:    function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {

255:    function updateTicker(string calldata _newTicker) external onlyDAO {

326:    function withdrawETHForKnot(address _recipient, bytes calldata _blsPublicKeyOfKnot) external {

495:    function isBLSPublicKeyPartOfLSDNetwork(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) {

500:    function isBLSPublicKeyBanned(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) {

645:    function _init(

695:    function _authorizeRepresentative(

739:    function _stake(

776:    function _joinLSDNStakehouse(

816:    function _createLSDNStakehouse(

879:    function _autoStakeWithSyndicate(address _associatedSmartWallet, bytes memory _blsPubKey) internal {

904:    function _initSavETHVault(address _savETHVaultDeployer, address _lpTokenFactory) internal virtual {

911:    function _initStakingFundsVault(address _stakingFundsVaultDeployer, address _tokenFactory) internal virtual {

921:    function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {

934:    function _assertEtherIsReadyForValidatorStaking(bytes calldata blsPubKey) internal view {

948:    function _updateDAORevenueCommission(uint256 _commissionPercentage) internal {
```
[File: SyndicateRewardsProcessor.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol)

```
30:    function _previewAccumulatedETH(

51:    function _distributeETHRewardsToUserForToken(

76:    function _updateAccumulatedETHPerLP(uint256 _numOfShares) internal {

93:    function totalRewardsReceived() public virtual view returns (uint256) {
```
## Unspecific Compiler Version Pragma
For most source-units the compiler version pragma is very unspecific ^0.8.13. While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different EVM compilation that is ultimately deployed on the blockchain.

Avoid floating pragmas where possible. It is highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.

## Sanity/Threshold/Limit Checks
Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider implementing a complete set of zero address/uint and/or empty string/bytes checks in the constructor. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors entailing immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values.

As an example, the following code block needing zero address checks may be refactored as follows:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L14-L16

```
    constructor(address _manager) {
        require(_manager != address(0), "Zero address detected.");
        liquidStakingManager = ILiquidStakingManager(_manager);
    }
```
Additionally, consider also adding an optional codehash check for addresses at the constructor since the zero address check cannot guarantee a matching address has been inputted.

All other instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L14-L16
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L14-L16
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L19-L27
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L16-L18
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L17-L20

## Immutable Variables
State variables having no setter functions associated and can only be assigned at the constructor should be declared immutable.

As an example, the following code block needing immutable visibility may be refactored as follows: ,

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L12-L16

```
    ILiquidStakingManager public immutable liquidStakingManager;

    constructor(address _manager) {
        liquidStakingManager = ILiquidStakingManager(_manager);
    }
```
All other instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L13-L16
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L13-L16
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15-L22
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15-L22
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L11-L27
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L13-L18
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L15-L68

## Descriptive Comments
Comments should be descriptive to convey its intended message rather than merely entailing abbreviations/symbols.

Here are the instances entailed:

[File: OwnableSmartWalletFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol)  

```
25:        emit WalletCreated(masterWallet, address(this)); // F: [OSWF-2]

29:        wallet = _createWallet(msg.sender); // F: [OSWF-1]

33:        wallet = _createWallet(owner); // F: [OSWF-1]

40:        IOwnableSmartWallet(wallet).initialize(owner); // F: [OSWF-1]

43:        emit WalletCreated(wallet, owner); // F: [OSWF-1]
```
[File: OwnableSmartWallet.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol)

```
31:        initializer // F: [OSW-1]

37:        _transferOwnership(initialOwner); // F: [OSW-1]

48:        return target.functionCallWithValue(callData, msg.value); // F: [OSW-6]

63:        return target.functionCallWithValue(callData, value); // F: [OSW-6]

90:        return Ownable.owner(); // F: [OSW-1]

108:            _setApproval(owner(), msg.sender, false); // F: [OSW-5]

110:        _transferOwnership(newOwner); // F: [OSW-5]

132:        _isTransferApproved[from][to] = status; // F: [OSW-2]

134:            emit TransferApprovalChanged(from, to, status); // F: [OSW-2]

145:        return from == to ? true : _isTransferApproved[from][to]; // F: [OSW-2, 3]
```
## Lines Too Long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible. 

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L40
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L46
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L35
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L222
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L35
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L381
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L222

## Modifier for Identical Checks
Similar/identical require statement used in different functions of the same contract should be grouped into a modifier.

Here is a contract instance entailed:

[File: GiantLP.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol)

```
30:        require(msg.sender == pool, "Only pool");

35:        require(msg.sender == pool, "Only pool");
```
## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are the instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L44-L45

```
        lastInteractedTimestamp[_from] = block.timestamp;
        lastInteractedTimestamp[_to] = block.timestamp;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L96

```
        require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L141

```
        bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;
```
[File: StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol)

```
184:        require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");

230:            require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
```
## No Storage Gap for Upgradeable Contracts
Consider adding a storage gap at the end of an upgradeable contract just in case it would entail some child contracts in the future that might introduce new variables. Devoid of a storage gap addition, when the upgradable contract introduces new variables, it may override the variables in the inheriting contract, leading to storage collisions.

Adding the following line of code would ensure no shifting down of storage in the inheritance chain of the inheriting contracts:

```
    uint256[50] private __gap;
```
Here are the contract instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

## Add a Constructor Initializer
As per Openzeppelin's recommendation:

https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/6

The guidelines are now to prevent front-running of `initialize()` on an implementation contract, by adding an empty constructor with the initializer modifier. Hence, the implementation contract gets initialized atomically upon deployment.

This feature is readily incorporated in the Solidity Wizard since the UUPS vulnerability discovery. You would just need to check UPGRADEABILITY to have the following constructor code block added to the contract:

```
    /// @custom:oz-upgrades-unsafe-allow constructor
    constructor() {
        _disableInitializers();
    }
```
Here are the contract instances with missing `_disableInitializers()`:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L27-L28
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L24-L25
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L42-L43
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L122-L123
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L165-L166

```
    /// @custom:oz-upgrades-unsafe-allow constructor
    constructor() initializer {}
```
## Failed Function Call Could Occur Without Contract Existence Check
Performing a low-level calls without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no function call was executed.

Here is one instance entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L78

```
        (bool result, bytes memory message) = target.call{value: value}(callData);
```
## Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to gas limitations or failed transactions. Consider bounding the loop length if the array is expected to be growing and/or handling a huge list of elements to avoid unnecessary gas wastage and denial of service.

Here are the instances entailed:

[File: GiantSavETHVaultPool.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol)

```
42:        for (uint256 i; i < numOfSavETHVaults; ++i) {

78:        for (uint256 i; i < numOfVaults; ++i) {

82:            for (uint256 j; j < _lpTokens[i].length; ++j) {

128:        for (uint256 i; i < numOfRotations; ++i) {

146:        for (uint256 i; i < numOfVaults; ++i) {

148:            for (uint256 j; j < _lpTokens[i].length; ++j) {
```
[File: SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)

```
63:        for (uint256 i; i < numOfValidators; ++i) {

103:        for (uint256 i; i < numOfTokens; ++i) {

116:        for (uint256 i; i < numOfTokens; ++i) {
```
[File: GiantMevAndFeesPool.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol)

```
38:        for (uint256 i; i < numOfVaults; ++i) {

64:        for (uint256 i; i < numOfVaults; ++i) {

90:        for (uint256 i; i < _stakingFundsVaults.length; ++i) {

117:        for (uint256 i; i < numOfRotations; ++i) {

135:        for (uint256 i; i < numOfVaults; ++i) {

183:        for (uint256 i; i < _lpTokens.length; ++i) {
```
[File: StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol)

```
78:        for (uint256 i; i < numOfValidators; ++i) {

152:        for (uint256 i; i < numOfTokens; ++i) {

166:        for (uint256 i; i < numOfTokens; ++i) {

203:        for (uint256 i; i < _blsPubKeys.length; ++i) {

266:        for (uint256 i; i < _blsPublicKeys.length; ++i) {

276:        for (uint256 i; i < _blsPubKeys.length; ++i) {

286:        for (uint256 i; i < _token.length; ++i) {
```
[File: Syndicate.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol)

```
211:        for (uint256 i; i < _blsPubKeys.length; ++i) {

259:        for (uint256 i; i < _blsPubKeys.length; ++i) {

301:        for (uint256 i; i < _blsPubKeys.length; ++i) {

346:        for (uint256 i; i < _blsPubKeys.length; ++i) {

420:        for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

513:                    for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

560:        for (uint256 i; i < knotsToRegister; ++i) {

585:        for (uint256 i; i < _priorityStakers.length; ++i) {

598:        for (uint256 i; i < _blsPublicKeys.length; ++i) {

648:        for (uint256 i; i < _blsPubKeys.length; ++i) {
```
[File: LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol)

```
392:        for(uint256 i; i < _blsPubKeys.length; ++i) {

465:        for(uint256 i; i < len; ++i) {

538:        for (uint256 i; i < numOfValidators; ++i) {

587:        for (uint256 i; i < numOfKnotsToProcess; ++i) {
```
## Un-indexed Parameters in Events
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data.

Here are the instances entailed:

[File: SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)

```
19:    event DETHRedeemed(address depositor, uint256 amount);

22:    event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

121:    event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```
[File: StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol)

```
25:    event ETHDeposited(address sender, uint256 amount);

28:    event ETHWithdrawn(address receiver, address admin, uint256 amount);

31:    event ERC20Recovered(address admin, address recipient, uint256 amount);

34:    event WETHUnwrapped(address admin, uint256 amount);
```
[File: Syndicate.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol)

```
42:    event UpdateAccruedETH(uint256 unprocessed);

45:    event CollateralizedSLOTReCalibrated(bytes BLSPubKey);

48:    event KNOTRegistered(bytes BLSPubKey);

51:    event KnotDeRegistered(bytes BLSPubKey);

54:    event PriorityStakerRegistered(address indexed staker);

57:    event Staked(bytes BLSPubKey, uint256 amount);

60:    event UnStaked(bytes BLSPubKey, uint256 amount);
```
## Empty Event
The following event has no parameter in it to emit anything. Consider refactoring or removing this unusable line of code.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L39

```
    event ContractDeployed();
```
## Open TODOs
Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Here is one instance entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L195

```
            // todo - check else case for any ETH lost
```
## Add a Timelock to Critical Parameter Change
It is a good practice to give time for users to react and adjust to critical changes with a mandatory time window between them. The first step merely broadcasts to users that a particular change is coming, and the second step commits that change after a suitable waiting period. This allows users that do not accept the change to withdraw within the grace period. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious Owner making any malicious or ulterior intention). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes.

Consider extending the timelock feature beyond contract ownership management to business critical functions. Here are some of the instances entailed:

[File: LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol)

```
239:    function updateDAOAddress(address _newAddress) external onlyDAO {

249:    function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {

255:    function updateTicker(string calldata _newTicker) external onlyDAO {

267:    function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {

278:    function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {

948:    function _updateDAORevenueCommission(uint256 _commissionPercentage) internal {
```
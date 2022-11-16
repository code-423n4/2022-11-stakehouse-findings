## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are the contract instances missing NatSpec in its entirety:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol

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

There are a total of four instances entailed:

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
## No Storage Gap for Upgradeable Contracts
Consider adding a storage gap at the end of an upgradeable contract just in case it would entail some child contracts in the future that might introduce new variables. Devoid of a storage gap addition, when the upgradable contract introduces new variables, it may override the variables in the inheriting contract, leading to storage collisions.

Adding the following line of code would ensure no shifting down of storage in the inheritance chain of the inheriting contracts:

```
    uint256[50] private __gap;
```
Here are the three contract instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol

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
Here are the three contract instances with missing `_disableInitializers()`:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L27-L28
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L24-L25
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L42-L43

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
[Line: SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)

```
63:        for (uint256 i; i < numOfValidators; ++i) {

103:        for (uint256 i; i < numOfTokens; ++i) {

116:        for (uint256 i; i < numOfTokens; ++i) {
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
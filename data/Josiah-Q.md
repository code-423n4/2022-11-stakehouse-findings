## USE OF BLOCK.TIMESTAMP
Some contract code uses the block.timestamp as part of the calculations and time checks. Nevertheless, timestamps can be slightly altered by miners/validators to favor them in contracts that have logic that depends strongly on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are the 6 instances found.

[GiantLP.sol#L44-L45](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L44-L45)

```
44:        lastInteractedTimestamp[_from] = block.timestamp;
45:        lastInteractedTimestamp[_to] = block.timestamp;
```
[GiantPoolBase.sol#L96](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L96)

```
96:        require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");
```
[SavETHVault.sol#L141](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L141)

```
141:        bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;
```
[StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol)

```
184:        require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");

230:            require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
```
## UNUSABLE EVENT
The following event is empty in the parameter field making it incapable of emitting anything. It is recommended removing this unusable line of code.

Here is 1 instance found.

[Syndicate.sol#L39](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L39)

```
39:    event ContractDeployed();
```
## MISSING NATSPEC
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

It is recommended using a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. 

Here are some of the `@param` deficient instances found amidst numerous cases throughout the code bases.

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
## SANITY CHECKS
Zero address checks implemented at the constructor could avoid human errors leading to non-functional calls associated with the mistakes. This is especially so when the incidents entail immutable variables preventing them from getting reassigned that could end up having the protocol redeploy the contract.

Here are the 6 instances found.

[OptionalHouseGatekeeper.sol#L15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L15)

```
15:        liquidStakingManager = ILiquidStakingManager(_manager);
```
[SavETHVaultDeployer.sol#L15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L15)

```
15:        implementation = address(new SavETHVault());
```
[StakingFundsVaultDeployer.sol#L15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L15)

```
15:        implementation = address(new StakingFundsVault());
```
[GiantLP.sol#L25-L26](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L25-L26)

```
25:        pool = _pool;
26:        transferHookProcessor = ITransferHookProcessor(_transferHookProcessor); 
```
[SyndicateFactory.sol#L17](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L17)

```
17:        syndicateImplementation = _syndicateImpl;
```
[GiantMevAndFeesPool.sol#L19](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L19)

```
19:        liquidStakingDerivativeFactory = _factory; // require(address(_factory) != address(0));
```
## CODEHASH CHECKS
It is recommended adding an optional and complementary codehash check for immutable addresses at the constructor since the zero address check cannot guarantee a matching/correct input address.

Here are the 9 instances found.

[LPTokenFactory.sol#L19](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L19)

```
19:        require(_lpTokenImplementation != address(0), "Address cannot be zero");
```
[LSDNFactory.sol#L51-L58](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L51-L58)

```
51:        require(_liquidStakingManagerImplementation != address(0), "Zero Address");
52:        require(_syndicateFactory != address(0), "Zero Address");
53:        require(_lpTokenFactory != address(0), "Zero Address");
54:        require(_smartWalletFactory != address(0), "Zero Address");
55:        require(_brand != address(0), "Zero Address");
56:        require(_savETHVaultDeployer != address(0), "Zero Address");
57:        require(_stakingFundsVaultDeployer != address(0), "Zero Address");
58:        require(_optionalGatekeeperDeployer != address(0), "Zero Address");
```
## IMMUTABLE VISIBILITY ON VARIABLES
State variables that are assigned at the constructor should be declared immutable if there are no setter functions associated with them.

Here are some of the missing `immutable` instances found amidst numerous cases throughout the code bases.

[LSDNFactory.sol#L15-L36](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L15-L36)

```
15:    address public liquidStakingManagerImplementation;

18:    address public syndicateFactory;

21:    address public lpTokenFactory;

24:    address public smartWalletFactory;

27:    address public brand;

30:    address public savETHVaultDeployer;

33:    address public stakingFundsVaultDeployer;

36:    address public optionalGatekeeperDeployer;
```
## DOS ON UNBOUNDED LOOP
Unbounded loop could lead to OOG (Out of Gas) denying the users of needed services. It is recommended setting a threshold for the array length if the array could grow to or entail an excessive size.

[StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol)

Here are some of the `for loop` instances found amidst numerous cases throughout the code bases.

```
78:        for (uint256 i; i < numOfValidators; ++i) {

152:        for (uint256 i; i < numOfTokens; ++i) {

166:        for (uint256 i; i < numOfTokens; ++i) {

203:        for (uint256 i; i < _blsPubKeys.length; ++i) {

266:        for (uint256 i; i < _blsPublicKeys.length; ++i) {

276:        for (uint256 i; i < _blsPubKeys.length; ++i) {

286:        for (uint256 i; i < _token.length; ++i) {
```
## TYPO ERRORS
[ETHPoolLPFactory.sol#L74](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L74)

```
    @ `Instane` should be corrected to `Instance`.
    /// @param _newLPToken Instane of the new LP token (to be minted)
```
[ETHPoolLPFactory.sol#L118](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L118)

```
    @ `it's` should be corrected `its`.
            // KNOT and it's LP token is already registered
```
[ETHPoolLPFactory.sol#L124](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L124)

```
    @ `depoistor` should be corrected to `depositor`.
            // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied
```
[ETHPoolLPFactory.sol#L150](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L150)

```
    @ `depoistor` should be corrected to `depositor`.
            // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied
```
## TIMELOCK FOR CRITICAL PARAMETER CHANGE
It is a good practice giving time to users to react and adjust to critical changes with a mandatory time window between the changes. The first step is simply broadcasting to users with a specific change that is coming whilst the second step commits that change after an appropriate period of waiting. This would allow time for users opposing to the change to withdraw within the set time frame. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of the owner making a malicious act). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes.

Here are some of the `updater` instances found.

[File: LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol)

```
239:    function updateDAOAddress(address _newAddress) external onlyDAO {

249:    function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {

255:    function updateTicker(string calldata _newTicker) external onlyDAO {

267:    function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {

278:    function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {

948:    function _updateDAORevenueCommission(uint256 _commissionPercentage) internal {
```
## COMPILER VERSION PRAGMA SPECIFICITY
Non-library contracts and interfaces should avoid using floating pragmas ^0.8.13. Doing this may be a security risk for the actual application implementation itself. For instance, a known vulnerable compiler version may accidentally be selected or a security tool might fallback to an older compiler version leading to checking a different EVM compilation that is ultimately deployed on the blockchain.

## USE OF ABBREVIATIONS IN COMMENTS
Comments involving abbreviations/symbols should have brief description attached along for better readability. 

Here are the 15 instances entailed.

[OwnableSmartWalletFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol) 

```
25:        emit WalletCreated(masterWallet, address(this)); // F: [OSWF-2]

29:        wallet = _createWallet(msg.sender); // F: [OSWF-1]

33:        wallet = _createWallet(owner); // F: [OSWF-1]

40:        IOwnableSmartWallet(wallet).initialize(owner); // F: [OSWF-1]

43:        emit WalletCreated(wallet, owner); // F: [OSWF-1]
```
[OwnableSmartWallet.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol)

```
31:        initializer // F: [OSW-1]

37:        _transferOwnership(initialOwner); // F: [OSW-1]

48:        return target.functionCallWithValue(callData, msg.value); // F: [OSW-6]

63:        return target.functionCallWithValue(callData, value); // F: [OSW-6]

90:        return Ownable.owner(); // F: [OSW-1]

108:       _setApproval(owner(), msg.sender, false); // F: [OSW-5]

110:       _transferOwnership(newOwner); // F: [OSW-5]

132:       _isTransferApproved[from][to] = status; // F: [OSW-2]

134:       emit TransferApprovalChanged(from, to, status); // F: [OSW-2]

145:       return from == to ? true : _isTransferApproved[from][to]; // F: [OSW-2, 3]
```
## COMMENT AND CODE LINE LENGTH
Try limiting the length of comments and/or code lines to 80 - 100 characters long for readability sake.

Here are some of the instances found.

[GiantLP.sol#L40](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L40)
[GiantLP.sol#L46](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L46)
[LSDNFactory.sol#L35](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L35)
[SavETHVault.sol#L222](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L222)
[StakingFundsVault.sol#L79](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79)
[StakingFundsVault.sol#L114](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114)
[Syndicate.sol#L35](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L35)
[Syndicate.sol#L381](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L381)
[LiquidStakingManager.sol#L222](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L222)

## MODIFIER ON REPEATED CONDITIONAL STATEMENTS
Repeated uses of the same require check in different function calls of the same contract could have a modifier implemented.

Here is 1 contract instance found.

[GiantLP.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol)

```
30:        require(msg.sender == pool, "Only pool");

35:        require(msg.sender == pool, "Only pool");
```
## STORAGE GAP FOR UPGRADEABLE CONTRACTS
Consider adding a storage gap at the end of each upgradeable contract. In the event some contracts needed to inherit from them, there would not be an issue shifting down of storage in the inheritance chain. Generally, storage gaps are a novel way of reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts. If not, the variable in the child contract might be overridden whenever new variables are added to it. This storage collision could have unintended and vulnerable consequences to the child contracts.

Here are the 5 contracts instances found.

[LPToken.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol)
[OwnableSmartWallet.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol)
[SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)
[Syndicate.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol)
[LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol)

## INCOMPLETE CONSTRUCTOR INITIALIZER
As stated in the Openzeppelin's forum below:

https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/6

"The guidelines are now to make it impossible for anyone to run `initialize` on an implementation contract, by adding an empty constructor with the `initializer` modifier. So the implementation contract gets initialized automatically upon deployment."

This feature has since been updated in the Solidity Wizard following the UUPS vulnerability discovery. You would just need to check UPGRADEABILITY to have the following constructor code block added to the contract:

```
    /// @custom:oz-upgrades-unsafe-allow constructor
    constructor() {
        _disableInitializers();
    }
```
Here are the contract instances with missing `_disableInitializers()`:

[LPToken.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol)
[OwnableSmartWallet.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol)
[SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)
[Syndicate.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol)
[LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol)

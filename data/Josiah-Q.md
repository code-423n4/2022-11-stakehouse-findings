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
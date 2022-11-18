# LOW

## 1. `_safeMint()` should be used rather than `_mint()` wherever possible

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both OpenZeppelin and solmate have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

```
31	_mint(_recipient, _amount);
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol
```
47	_mint(_recipient, _amount);
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol


## 2. Unused/empty receive()/fallback() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. `require(msg.sender == address(weth))`). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

```
629      receive() external payable {}
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol
```
98       receive() external payable {}
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol


# NON-CRITICAL

## 1. Non-library/interface files should use fixed compiler versions, not floating ones

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

```
Amost all contracts use floating Solidity version
```


## 2. Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

```
195      // todo - check else case for any ETH lost
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol


## 3. `public` functions not called by the contract should be declared `external` instead

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from `external` to `public` and can save gas by doing so

```
73	function deployNewLiquidStakingDerivativeNetwork(
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol
```
29	function batchDepositETHForStaking(
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol
```
200	function withdrawETHForStaking(
218	function isBLSPublicKeyPartOfLSDNetwork(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol
```
176	function totalRewardsReceived() public view override returns (uint256) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol
```
239	function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol
```
458	function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol
```
514	function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol


## 4. The `nonReentrant modifier` should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers

```
203      ) public onlyManager nonReentrant returns (uint256) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol
```
239      function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
259      ) external onlyManager nonReentrant {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol


## 5. Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```
13	event ETHDeposited(address indexed sender, uint256 amount);
16	event LPBurnedForETH(address indexed sender, uint256 amount);
19	event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol
```
16	event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol
```
19	event DETHRedeemed(address depositor, uint256 amount);
22	event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
121	event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol
```
25	event ETHDeposited(address sender, uint256 amount);
28	event ETHWithdrawn(address receiver, address admin, uint256 amount);
31	event ERC20Recovered(address admin, address recipient, uint256 amount);
34	event WETHUnwrapped(address admin, uint256 amount);
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol
```
42	event UpdateAccruedETH(uint256 unprocessed);
45	event CollateralizedSLOTReCalibrated(bytes BLSPubKey);
48	event KNOTRegistered(bytes BLSPubKey);
51	event KnotDeRegistered(bytes BLSPubKey);
57	event Staked(bytes BLSPubKey, uint256 amount);
60	event UnStaked(bytes BLSPubKey, uint256 amount);
63	event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol
```
36	event WhitelistingStatusChanged(address indexed dao, bool updatedStatus);
39	event NodeRunnerWhitelistingStatusChanged(address indexed nodeRunner, bool updatedStatus);
48	event WalletCredited(address indexed smartWallet, uint256 amount);
51	event KnotStaked(bytes _blsPublicKeyOfKnot, address indexed trigerringAddress);
54	event StakehouseCreated(string stakehouseTicker, address indexed stakehouse);
57	event StakehouseJoined(bytes blsPubKey);
63	event DormantRepresentative(address indexed associatedSmartWallet, address representative);
66	event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);
69	event NetworkTickerUpdated(string newTicker);
84	event DAOCommissionUpdated(uint256 old, uint256 newCommission);
87	event NewLSDValidatorRegistered(address indexed nodeRunner, bytes blsPublicKey);
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol
```
16	event ETHWithdrawnByDepositor(address depositor, uint256 amount);
19	event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
22	event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
25	event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol

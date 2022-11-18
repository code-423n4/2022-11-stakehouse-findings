## 1. `require()`/`revert()` strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs 3 gas

```
122      require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
130      require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol
```
55       require(idleETH >= _amount, "Come back later or withdraw less ETH");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol
```
256      require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
257      require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
258      require(numberOfKnots == 0, "Cannot change ticker once house is created");
268      require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
280      require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
291      require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
328      require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
331      require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");
332      require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
393      require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
396      require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");
435      require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");
437      require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
455      require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
469      require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
541      require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
589      require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
662      require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
663      require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
936      require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
939      require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
940      require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
944      require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol
```
64       require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
84       require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
90       require(msg.value == _amount, "Must provide correct amount of ETH");
204      require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol
```
79       require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
114      require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
120      require(msg.value == _amount, "Must provide correct amount of ETH");
154      require(address(token) != address(0), "No ETH staked for specified BLS key");
240      require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
267      require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol


## 2. Splitting `require()` statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions,use multiple require statements with 1 condition per require statement.

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

```
357      require(_new != address(0) && _current != _new, "New is zero or current");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol


## 3. \<x\> += \<y\>` costs more gas than `\<x\> = \<x\> + \<y\>` for state variables


```
39       idleETH += msg.value;
57       idleETH -= _amount
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol
```
782      numberOfKnots += 1;
839      numberOfKnots += 1;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol
```
58	totalShares += 4 ether;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol
```
190      accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
225      totalFreeFloatingShares += _sETHAmount;
226      sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
227      sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
269      totalFreeFloatingShares -= _sETHAmount;
272      sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
273      sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;
317      totalClaimed += unclaimedUserShare;
511      accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;
521      accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
558      numberOfRegisteredKnots += knotsToRegister;
621      totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];
624      numberOfRegisteredKnots -= 1;
658      totalClaimed += unclaimedUserShare;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol


## 4. Boolean comparisons

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value.

```
291      require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
328      require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
332      require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
393      require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
436      require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
437      require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
469      require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
541      require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
589      require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
688      require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol
```
64       require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
84       require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol
```
79       require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
114      require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
205      liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol
```
611      if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();
612      if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol


## 5. Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`). Empty `receive()`/`fallback()` payable functions that are not used, can be removed to save deployment gas.

```
100      function _onDepositETH() internal virtual {}
103      function _onWithdraw(LPToken[] calldata _lpTokens) internal virtual {}
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol
```
28       constructor() initializer {}
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol
```
25       constructor() initializer {}
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol
```
43       constructor() initializer {}
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol
```
43       constructor() initializer {}
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol
```
123      constructor() initializer {}
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol
```
98       receive() external payable {}
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol


## 6. `public` functions not called by the contract should be declared `external` instead

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


## 7. Constructor parameters should be avoided when possible

Constructor parameters are expensive. The contract deployment will be cheaper in gas if they are hard coded instead of using constructor parameters.

```
60	liquidStakingManagerImplementation = _liquidStakingManagerImplementation;
61	syndicateFactory = _syndicateFactory;
62	lpTokenFactory = _lpTokenFactory;
63	smartWalletFactory = _smartWalletFactory;
64	brand = _brand;
65	savETHVaultDeployer = _savETHVaultDeployer;
66	stakingFundsVaultDeployer = _stakingFundsVaultDeployer;
67	optionalGatekeeperDeployer = _optionalGatekeeperDeployer;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol
```
17	syndicateImplementation = _syndicateImpl;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol
```
25	pool = _pool;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol
```
21	lpTokenImplementation = _lpTokenImplementation;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol


## 8. Multiple `address`/`ID` mappings can be combined into a single `mapping `of an `address`/`ID` to a `struct`, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key’s keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

```
123	mapping(address => bool) public isNodeRunnerWhitelisted;
126	mapping(address => address) public smartWalletRepresentative;
132	mapping(address => address) public smartWalletOfNodeRunner;
135	mapping(address => address) public nodeRunnerOfSmartWallet;
138	mapping(address => uint256) public stakedKnotsOfSmartWallet;
141	mapping(address => address) public smartWalletDormantRepresentative;
149	mapping(address => bool) public bannedNodeRunners;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol
```
90	mapping(bytes => bool) public isKnotRegistered;
99	mapping(bytes => uint256) public sETHTotalStakeForKnot;
102	mapping(bytes => mapping(address => uint256)) public sETHStakedBalanceForKnot;
105	mapping(bytes => mapping(address => uint256)) public sETHUserClaimForKnot;
108	mapping(bytes => uint256) public totalETHProcessedPerCollateralizedKnot;
111	mapping(bytes => mapping(address => uint256)) public accruedEarningPerCollateralizedSlotOwnerOfKnot;
114	mapping(bytes => mapping(address => uint256)) public claimedPerCollateralizedSlotOwnerOfKnot;
117	mapping(bytes => bool) public isNoLongerPartOfSyndicate;
120	mapping(bytes => uint256) public lastAccumulatedETHPerFreeFloatingShare;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

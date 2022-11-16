

### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::32 => uint256 public numberOfLPTokensIssued;
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::35 => uint256 public maxStakingAmountPerValidator;
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::38 => uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::41 => LPTokenFactory public lpTokenFactory;
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::11 => address public pool;
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::14 => ITransferHookProcessor public transferHookProcessor;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::25 => uint256 public idleETH;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::28 => GiantLP public lpTokenETH;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::31 => LSDNFactory public liquidStakingDerivativeFactory;
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::14 => address public deployer;
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::15 => address public lpTokenImplementation;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::15 => address public liquidStakingManagerImplementation;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::18 => address public syndicateFactory;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::21 => address public lpTokenFactory;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::24 => address public smartWalletFactory;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::27 => address public brand;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::30 => address public savETHVaultDeployer;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::33 => address public stakingFundsVaultDeployer;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::36 => address public optionalGatekeeperDeployer;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::90 => address public brand;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::93 => address public override stakehouse;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::96 => address public syndicate;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::99 => address public dao;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::102 => OptionalHouseGatekeeper public gatekeeper;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::105 => ISyndicateFactory public syndicateFactory;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::108 => IOwnableSmartWalletFactory public smartWalletFactory;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::111 => string public stakehouseTicker;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::114 => StakingFundsVault public stakingFundsVault;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::117 => SavETHVault public savETHVault;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::120 => bool public enableWhitelisting;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::152 => uint256 public numberOfKnots;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::155 => uint256 public daoCommissionPercentage;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::158 => uint256 public MODULO = 100_00000;
2022-11-stakehouse/contracts/liquid-staking/OptionalHouseGatekeeper.sol::12 => ILiquidStakingManager public liquidStakingManager;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::25 => ILiquidStakingManager public liquidStakingManager;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::28 => uint256 public indexOwnedByTheVault;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::37 => LiquidStakingManager public liquidStakingNetworkManager;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::40 => uint256 public totalShares;
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::18 => uint256 public accumulatedETHPerLPShare;
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::21 => uint256 public totalClaimed;
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::24 => uint256 public totalETHSeen;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::69 => uint256 public accumulatedETHPerFreeFloatingShare;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::72 => uint256 public accumulatedETHPerCollateralizedSlotPerKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::75 => uint256 public lastSeenETHPerCollateralizedSlotPerKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::78 => uint256 public lastSeenETHPerFreeFloating;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::81 => uint256 public totalFreeFloatingShares;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::84 => uint256 public totalClaimed;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::87 => uint256 public numberOfRegisteredKnots;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::93 => uint256 public priorityStakingEndBlock;
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::13 => address public syndicateImplementation;
```




### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::11 => address public pool;
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::14 => address public deployer;
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::15 => address public lpTokenImplementation;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::15 => address public liquidStakingManagerImplementation;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::18 => address public syndicateFactory;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::21 => address public lpTokenFactory;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::24 => address public smartWalletFactory;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::27 => address public brand;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::30 => address public savETHVaultDeployer;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::33 => address public stakingFundsVaultDeployer;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::36 => address public optionalGatekeeperDeployer;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::90 => address public brand;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::93 => address public override stakehouse;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::96 => address public syndicate;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::99 => address public dao;
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::13 => address public syndicateImplementation;
```


### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
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




### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::63 => for (uint256 i; i < numOfRotations; ++i) {
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::144 => numberOfLPTokensIssued++;
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::38 => for (uint256 i; i < numOfVaults; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::64 => for (uint256 i; i < numOfVaults; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::90 => for (uint256 i; i < _stakingFundsVaults.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::117 => for (uint256 i; i < numOfRotations; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::135 => for (uint256 i; i < numOfVaults; ++i) {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::177 => return address(this).balance + totalClaimed - idleETH;
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
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::770 => stakedKnotsOfSmartWallet[smartWallet] += 1;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::63 => for (uint256 i; i < numOfValidators; ++i) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::103 => for (uint256 i; i < numOfTokens; ++i) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::116 => for (uint256 i; i < numOfTokens; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::58 => totalShares += 4 ether;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::78 => for (uint256 i; i < numOfValidators; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::152 => for (uint256 i; i < numOfTokens; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::166 => for (uint256 i; i < numOfTokens; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::203 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::266 => for (uint256 i; i < _blsPublicKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::276 => for (uint256 i; i < _blsPubKeys.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::286 => for (uint256 i; i < _token.length; ++i) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::287 => totalAccumulated += previewAccumulatedETH(_user, _token[i]);
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




### [G05] `require()`/`revert()` strings longer than 32 bytes cost extra gas


#### Findings:
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




### [G06] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::97 => return _previewAccumulatedETH(_user, address(lpTokenETH), lpTokenETH.balanceOf(_user), lpTokenETH.totalSupply(), accumulated);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::93 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::193 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::214 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::142 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::249 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::302 => return _previewAccumulatedETH(
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::454 => return _calculateNewAccumulatedETHPerFreeFloatingShare(ethSinceLastUpdate);
```




### [G07] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement



#### Findings:
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
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::414 => if (daoAmount > 0) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::532 => require(numOfValidators > 0, "No data");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::583 => require(numOfKnotsToProcess > 0, "Empty array");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::922 => require(_received > 0, "Nothing received");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::924 => if (daoCommissionPercentage > 0) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::59 => require(numOfValidators > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::101 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::114 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::71 => require(numOfValidators > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::150 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::164 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::320 => require(blsPubKey.length > 0, "Invalid token");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::346 => require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::37 => if (_balanceOfSender > 0) {
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::59 => if (balance > 0) {
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::62 => if (due > 0) {
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::77 => if (_numOfShares > 0) {
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::81 => if (unprocessed > 0) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::177 => if (numberOfRegisteredKnots > 0) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::183 => if (totalFreeFloatingShares > 0) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::315 => if (unclaimedUserShare > 0) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::500 => if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::551 => return totalFreeFloatingShares > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingShares : 0;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::588 => if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::634 => lastAccumulatedETHPerFreeFloatingShare[_blsPublicKey] > 0 ?
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::656 => if (unclaimedUserShare > 0) {
```



### [G08] Splitting `require()` statements that use `&&` Cost gas


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::357 => require(_new != address(0) && _current != _new, "New is zero or current");
```



### [G09] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::30 => require(msg.sender == pool, "Only pool");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::62 => require(numOfVaults > 0, "Empty array");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::171 => require(msg.sender == address(lpTokenETH), "Caller is not giant LP");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::53 => require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::72 => require(numOfVaults > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::73 => require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::74 => require(numOfVaults == _amounts.length, "Inconsistent arrays");
```




### [G10] `require()` or `revert()` statements that check input arguments should be at the top of the function

#### Impact
Checks that involve constants should come before checks that involve state variables
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::77 => require(address(_oldLPToken) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::78 => require(address(_newLPToken) != address(0), "Zero address");
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
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::544 => require(associatedSmartWallet != address(0), "Unknown BLS public key");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::658 => require(_dao != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::659 => require(_syndicateFactory != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::660 => require(_smartWalletFactory != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::661 => require(_brand != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::685 => require(_nodeRunner != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::939 => require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::943 => require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::206 => require(_smartWallet != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::236 => require(_liquidStakingManagerAddress != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::237 => require(address(_lpTokenFactory) != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::154 => require(address(token) != address(0), "No ETH staked for specified BLS key");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::177 => require(address(_lpToken) != address(0), "Zero address specified");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::229 => require(address(token) != address(0), "Invalid BLS key");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::242 => require(_wallet != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::361 => require(_syndicate != address(0), "Invalid configuration");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::372 => require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::373 => require(address(_lpTokenFactory) != address(0), "Zero Address");
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::57 => require(_recipient != address(0), "Zero address");
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::37 => require(owner != address(0), 'Wallet cannot be address 0');
```




### [G11] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
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
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::91 => require(
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::96 => require(
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::111 => require(_amount >= MIN_STAKING_AMOUNT, "Min amount not reached");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::112 => require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::122 => require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::130 => require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::30 => require(msg.sender == pool, "Only pool");
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::35 => require(msg.sender == pool, "Only pool");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::35 => require(numOfVaults > 0, "Zero vaults");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::36 => require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::37 => require(numOfVaults == _amounts.length, "Inconsistent lengths");
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::43 => require(
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
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::49 => require(
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
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::149 => require(
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
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::334 => require(
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
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::471 => require(
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::532 => require(numOfValidators > 0, "No data");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::533 => require(numOfValidators == _ciphertexts.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::534 => require(numOfValidators == _aesEncryptorKeys.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::535 => require(numOfValidators == _encryptionSignatures.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::536 => require(numOfValidators == _dataRoots.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::541 => require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::544 => require(associatedSmartWallet != address(0), "Unknown BLS public key");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::545 => require(
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::583 => require(numOfKnotsToProcess > 0, "Empty array");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::584 => require(numOfKnotsToProcess == _beaconChainBalanceReports.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::585 => require(numOfKnotsToProcess == _reportSignatures.length, "Inconsistent array lengths");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::589 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::592 => require(
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::658 => require(_dao != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::659 => require(_syndicateFactory != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::660 => require(_smartWalletFactory != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::661 => require(_brand != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::662 => require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::663 => require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::685 => require(_nodeRunner != address(0), "Zero address");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::688 => require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::734 => revert("Unexpected state");
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
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::65 => require(
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::76 => require(msg.value == totalAmount, "Invalid ETH amount attached");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::84 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::85 => require(
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::90 => require(msg.value == _amount, "Must provide correct amount of ETH");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::101 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::102 => require(numOfTokens == _amounts.length, "Inconsistent array length");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::114 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::115 => require(numOfTokens == _amounts.length, "Inconsisent array length");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::127 => require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::128 => require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::134 => require(
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
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::80 => require(
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::107 => require(msg.value == totalAmount, "Invalid ETH amount attached");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::114 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::115 => require(
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::120 => require(msg.value == _amount, "Must provide correct amount of ETH");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::150 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::151 => require(numOfTokens == _amounts.length, "Inconsistent array length");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::154 => require(address(token) != address(0), "No ETH staked for specified BLS key");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::164 => require(numOfTokens > 0, "Empty arrays");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::165 => require(numOfTokens == _amounts.length, "Inconsistent array length");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::175 => require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::176 => require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::177 => require(address(_lpToken) != address(0), "Zero address specified");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::180 => require(
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::184 => require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::191 => require(result, "Transfer failed");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::204 => require(
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::210 => require(
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
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::33 => require(
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::79 => require(result, "Failed to execute");
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::100 => require(
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::115 => require(
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::37 => require(owner != address(0), 'Wallet cannot be address 0');
```




### [G12] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
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




### [G13] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.15 to have external calls skip
 contract existence checks if the external call has a return value
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/OptionalGatekeeperFactory.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/OptionalHouseGatekeeper.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/SavETHVaultDeployer.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVaultDeployer.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::1 => pragma solidity 0.8.13;
2022-11-stakehouse/contracts/syndicate/SyndicateErrors.sol::1 => pragma solidity 0.8.13;
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::1 => pragma solidity 0.8.13;
```




### [G14] Bitshift for divide by 2

#### Impact
When multiplying or dividing by a power of two, it is cheaper to bitshift than to use standard math operations. 
#### Findings:
```
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::378 => return ethPerKnot / 2;
```





### [G15] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.

#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::879 => function _autoStakeWithSyndicate(address _associatedSmartWallet, bytes memory _blsPubKey) internal {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::355 => function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::360 => function _claimFundsFromSyndicateForDistribution(address _syndicate, bytes[] memory _blsPubKeys) internal {
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::41 => function execute(address target, bytes memory callData)
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::337 => function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::343 => function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::359 => function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::491 => function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::555 => function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots) internal {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::583 => function _addPriorityStakers(address[] memory _priorityStakers) internal {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::610 => function _deRegisterKnot(bytes memory _blsPublicKey) internal {
```




### [G16] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done in some places, it’s not consistently done in the solution.
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::184 => _lpTokens[i].transfer(address(this), _lpTokens[i].balanceOf(address(this)));
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::86 => token.transfer(msg.sender, amount);
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::108 => getDETH().transfer(msg.sender, dETHReceivedFromAllSavETHVaults);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::233 => bool transferResult = sETH.transferFrom(msg.sender, address(this), _sETHAmount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::275 => bool transferResult = sETH.transfer(_sETHRecipient, _sETHAmount);
```


### [G17] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::123 => mapping(address => bool) public isNodeRunnerWhitelisted;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::126 => mapping(address => address) public smartWalletRepresentative;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::129 => mapping(bytes => address) public smartWalletOfKnot;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::132 => mapping(address => address) public smartWalletOfNodeRunner;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::135 => mapping(address => address) public nodeRunnerOfSmartWallet;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::138 => mapping(address => uint256) public stakedKnotsOfSmartWallet;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::141 => mapping(address => address) public smartWalletDormantRepresentative;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::145 => mapping(bytes => address) public bannedBLSPublicKeys;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::149 => mapping(address => bool) public bannedNodeRunners;

2022-11-stakehouse/contracts/syndicate/Syndicate.sol::99 => mapping(bytes => uint256) public sETHTotalStakeForKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::102 => mapping(bytes => mapping(address => uint256)) public sETHStakedBalanceForKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::105 => mapping(bytes => mapping(address => uint256)) public sETHUserClaimForKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::108 => mapping(bytes => uint256) public totalETHProcessedPerCollateralizedKnot;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::111 => mapping(bytes => mapping(address => uint256)) public accruedEarningPerCollateralizedSlotOwnerOfKnot;
```




### [G18] Remove unused local variable


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::60 => (bool success,) = msg.sender.call{value: _amount}("");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::411 => (bool transferResult, ) = _recipient.call{value: nodeRunnerAmount}("");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::460 => (bool result,) = smartWallet.call{value: msg.value}("");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::189 => (bool result,) = msg.sender.call{value: _amount}("");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::209 => (bool result,) = _smartWallet.call{value: _amount}("");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::190 => (bool result,) = msg.sender.call{value: _amount}("");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::244 => (bool result,) = _wallet.call{value: _amount}("");
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::67 => (bool success, ) = _recipient.call{value: due}("");
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::321 => (bool success,) = _recipient.call{value: unclaimedUserShare}("");
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::668 => (bool success,) = _recipient.call{value: unclaimedUserShare}("");
```




### [G19] Using `bools` for storage incurs overhead


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::39 => mapping(address => bool) public isLiquidStakingManager;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::120 => bool public enableWhitelisting;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::123 => mapping(address => bool) public isNodeRunnerWhitelisted;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::149 => mapping(address => bool) public bannedNodeRunners;
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::22 => mapping(address => mapping(address => bool)) internal _isTransferApproved;
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::20 => mapping(address => bool) public walletExists;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::90 => mapping(bytes => bool) public isKnotRegistered;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::96 => mapping(address => bool) public isPriorityStaker;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::117 => mapping(bytes => bool) public isNoLongerPartOfSyndicate;
```


### [G20] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable getter functions for deployment call data, and not adding another entry to the method ID table
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::38 => uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::22 => uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::15 => uint256 public constant PRECISION = 1e24;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::66 => uint256 public constant PRECISION = 1e24;
```




### [G21] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
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






### [G22] `abi.encode()` is less efficient than abi.encodePacked()



#### Findings:
```
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::63 => return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));
```




### [G23] Don’t compare boolean expressions to boolean literals

#### Impact
`if (<x> == true)` => `if (<x>), if (<x> == false)` => `if (!<x>)`
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::150 => vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::291 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::328 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::332 => require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::393 => require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::436 => require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::437 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::469 => require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::541 => require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::589 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::688 => require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::64 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::84 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::79 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::114 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::205 => liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::611 => if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::612 => if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
```


### [G24] Optimize names to save gas

#### Impact
public/external function names and public member variable names can be optimized to save gas. See [this
 link](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::13 => abstract contract ETHPoolLPFactory is StakehouseAPI {
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::6 => abstract contract SyndicateRewardsProcessor {
```



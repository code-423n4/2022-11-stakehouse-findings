



### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::25 => pool = _pool;
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::38 => deployer = _deployer;
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::21 => lpTokenImplementation = _lpTokenImplementation;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::60 => liquidStakingManagerImplementation = _liquidStakingManagerImplementation;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::61 => syndicateFactory = _syndicateFactory;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::62 => lpTokenFactory = _lpTokenFactory;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::63 => smartWalletFactory = _smartWalletFactory;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::64 => brand = _brand;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::65 => savETHVaultDeployer = _savETHVaultDeployer;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::66 => stakingFundsVaultDeployer = _stakingFundsVaultDeployer;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::67 => optionalGatekeeperDeployer = _optionalGatekeeperDeployer;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::665 => brand = _brand;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::666 => dao = _dao;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::669 => stakehouseTicker = _stakehouseTicker;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::239 => lpTokenFactory = _lpTokenFactory;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::375 => liquidStakingNetworkManager = _liquidStakingNetworkManager;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::376 => lpTokenFactory = _lpTokenFactory;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::483 => priorityStakingEndBlock = _priorityStakingEndBlock;
```



### [L02] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::28 => constructor() initializer {}
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::37 => ) external override initializer {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::166 => constructor() initializer {}
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::181 => ) external virtual override initializer {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::43 => constructor() initializer {}
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::45 => function init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) external virtual initializer {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::43 => constructor() initializer {}
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::46 => function init(address _liquidStakingNetworkManager, LPTokenFactory _lpTokenFactory) external virtual initializer {
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::25 => constructor() initializer {}
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::28 => function initialize(address initialOwner)
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::31 => initializer // F: [OSW-1]
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::35 => "OwnableSmartWallet: Attempting to initialize with zero address owner"
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::40 => IOwnableSmartWallet(wallet).initialize(owner); // F: [OSWF-1]
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::123 => constructor() initializer {}
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::129 => function initialize(
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::134 => ) external virtual override initializer {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::135 => _initialize(
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::469 => function _initialize(
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::34 => ISyndicateInit(newInstance).initialize(
```



### [L03] Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

#### Impact
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::11 => contract LPToken is ILPTokenInit, ILiquidStakingManagerChildContract, Initializable, ERC20PermitUpgradeable {
```



### [L04] Unused `receive()` function will lock Ether in contract

#### Impact
If the intention is for the Ether to be used, the function should call another function, otherwise, it should revert
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::629 => receive() external payable {}
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::98 => receive() external payable {}
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::148 => receive() external payable {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::352 => receive() external payable {
```

### [L05] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

#### Impact
approve is subject to a known front-running attack. Consider using safeApprove instead:

#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::870 => sETH.approve(syndicate, (2 ** 256) - 1);
```


### [L06] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.
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
```





````````````
#### Non-Critical Issues

````````````


### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::47 => return newInstance;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::101 => return newInstance;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::272 => return enableWhitelisting;
2022-11-stakehouse/contracts/liquid-staking/OptionalGatekeeperFactory.sol::16 => return newKeeper;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::93 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::182 => return redemptionValue;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::193 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::214 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/SavETHVaultDeployer.sol::24 => return newVault;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::142 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::249 => return _amount;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::280 => return totalAccumulated;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::289 => return totalAccumulated;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVaultDeployer.sol::24 => return newVault;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::541 => return collateralizedSLOTShareOfETHPerKnot;
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::44 => return newInstance;
```



### [N02] constants should be defined rather than using magic numbers


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::434 => require(msg.value == len * 4 ether, "Insufficient ether provided");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::870 => sETH.approve(syndicate, (2 ** 256) - 1);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::174 => redemptionValue = (dETHDetails.savETHBalance * _amount) / 24 ether;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::431 => balance * unprocessedForKnot / (4 ether - currentSlashedAmount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::522 => balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::546 => return (_ethSinceLastUpdate * PRECISION) / (numberOfRegisteredKnots * 4 ether);
```


### [N03] Variable names that consist of all capital letters should be reserved for `const`/`immutable `variables

#### Impact
If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::28 => string internal baseLPTokenName;
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::29 => string internal baseLPTokenSymbol;
```


### [N04] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::16 => event ETHWithdrawnByDepositor(address depositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::19 => event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::22 => event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::25 => event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::13 => event ETHDeposited(address indexed sender, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::16 => event LPBurnedForETH(address indexed sender, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::19 => event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::16 => event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::36 => event WhitelistingStatusChanged(address indexed dao, bool updatedStatus);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::39 => event NodeRunnerWhitelistingStatusChanged(address indexed nodeRunner, bool updatedStatus);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::42 => event SmartWalletCreated(address indexed smartWallet, address indexed nodeRunner);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::45 => event RepresentativeAppointed(address indexed smartWallet, address indexed eoaRepresentative);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::48 => event WalletCredited(address indexed smartWallet, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::51 => event KnotStaked(bytes _blsPublicKeyOfKnot, address indexed trigerringAddress);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::54 => event StakehouseCreated(string stakehouseTicker, address indexed stakehouse);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::57 => event StakehouseJoined(bytes blsPubKey);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::60 => event RepresentativeRemoved(address indexed smartWallet, address indexed eoaRepresentative);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::63 => event DormantRepresentative(address indexed associatedSmartWallet, address representative);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::66 => event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::72 => event NodeRunnerRewardsClaimed(address indexed nodeRunner, address indexed recipient);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::75 => event NodeRunnerOfSmartWalletRotated(address indexed wallet, address indexed oldRunner, address indexed newRunner);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::87 => event NewLSDValidatorRegistered(address indexed nodeRunner, bytes blsPublicKey);
2022-11-stakehouse/contracts/liquid-staking/OptionalGatekeeperFactory.sol::9 => event NewOptionalGatekeeperDeployed(address indexed keeper, address indexed manager);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::19 => event DETHRedeemed(address depositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::22 => event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::121 => event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
2022-11-stakehouse/contracts/liquid-staking/SavETHVaultDeployer.sol::11 => event NewVaultDeployed(address indexed instance);
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::25 => event ETHDeposited(address sender, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::28 => event ETHWithdrawn(address receiver, address admin, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::31 => event ERC20Recovered(address admin, address recipient, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::34 => event WETHUnwrapped(address admin, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::12 => event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::57 => event Staked(bytes BLSPubKey, uint256 amount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::60 => event UnStaked(bytes BLSPubKey, uint256 amount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::63 => event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);
```


### [N05] States/flags should use Enums rather than separate constants


#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::38 => uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::22 => uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::15 => uint256 public constant PRECISION = 1e24;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::66 => uint256 public constant PRECISION = 1e24;
```



### [N06] Unused file

#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/OptionalGatekeeperFactory.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/OptionalHouseGatekeeper.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/SavETHVaultDeployer.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVaultDeployer.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/syndicate/SyndicateErrors.sol::3 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::3 => // SPDX-License-Identifier: MIT
```






### [N07] `public` functions not called by the contract should be declared `external` instead


#### Findings:
```

2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::76 => function rotateLPTokens(LPToken _oldLPToken, LPToken _newLPToken, uint256 _amount) public {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::141 => function updateAccumulatedETHPerLP() public {
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::176 => function totalRewardsReceived() public view override returns (uint256) {
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::34 => function depositETH(uint256 _amount) public payable {
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::34 => ) public {
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::78 => ) public returns (address) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::495 => function isBLSPublicKeyPartOfLSDNetwork(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::500 => function isBLSPublicKeyBanned(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::507 => function isNodeRunnerBanned(address _nodeRunner) public view returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::514 => function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::83 => function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::126 => function burnLPToken(LPToken _lpToken, uint256 _amount) public nonReentrant returns (uint256) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::203 => ) public onlyManager nonReentrant returns (uint256) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::218 => function isBLSPublicKeyPartOfLSDNetwork(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::223 => function isBLSPublicKeyBanned(bytes calldata _blsPublicKeyOfKnot) public view returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::62 => function updateAccumulatedETHPerLP() public {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::113 => function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::174 => function burnLPForETH(LPToken _lpToken, uint256 _amount) public nonReentrant {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::239 => function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::293 => function previewAccumulatedETH(address _user, LPToken _token) public view returns (uint256) {
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::93 => function totalRewardsReceived() public virtual view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::174 => function updateAccruedETHPerShares() public {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::285 => function claimAsStaker(address _recipient, bytes[] calldata _blsPubKeys) public nonReentrant {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::359 => function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::373 => function calculateETHForFreeFloatingOrCollateralizedHolders() public view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::441 => function getUnprocessedETHForAllFreeFloatingSlot() public view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::446 => function getUnprocessedETHForAllCollateralizedSlot() public view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::452 => function calculateNewAccumulatedETHPerFreeFloatingShare() public view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::458 => function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::464 => function totalETHReceived() public view returns (uint256) {
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::26 => ) public override returns (address) {
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::62 => ) public override pure returns (bytes32) {
```



### [N08] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::82 => require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::96 => require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::775 => /// @dev The second knot onwards will join the LSDN stakehouse and expand the registered syndicate knots
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::141 => bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::184 => require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::230 => require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
```

### [N09] NC-library/interface files should use fixed compiler versions, not floating ones


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






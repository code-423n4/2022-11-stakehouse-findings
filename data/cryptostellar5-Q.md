
### (01) UPGRADEABLE CONTRACT IS MISSING A `__GAP[50]` STORAGE VARIABLE TO ALLOW FOR NEW STORAGE VARIABLES IN LATER VERSIONS

*Number of Instances Identified: 1*

See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol

```
11: contract LPToken is ILPTokenInit, ILiquidStakingManagerChildContract, Initializable, ERC20PermitUpgradeable { 

    /// @notice Contract deployer that can control minting and burning but is associated with a liquid staking manager
    address public deployer;

    /// @notice Optional hook for processing transfers
    ITransferHookProcessor transferHookProcessor;

    /// @notice Whenever the address last interacted with a token
    mapping(address => uint256) public lastInteractedTimestamp;

    modifier onlyDeployer {
        require(msg.sender == deployer, "Only savETH vault");
        _;
    }
```


### (02) MISSING EVENTS FOR ONLY FUNCTIONS THAT CHANGE CRITICAL PARAMETERS

The functions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

*Number of Instances Identified: 3*

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
202: function executeAsSmartWallet
218: function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO
226: function restoreFreeFloatingSharesToSmartWalletForRageQuit
```

### (03) `safeIncreaseAllowance` AND `safeDecreaseAllowance` INSTEAD OF `approve`

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
870: sETH.approve(syndicate, (2 ** 256) - 1);
```


### (04) PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

*Number of Instances Identified: 4*

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
514: function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol

```
73: function deployNewLiquidStakingDerivativeNetwork( 
        address _dao,
        uint256 _optionalCommission,
        bool _deployOptionalHouseGatekeeper,
        string calldata _stakehouseTicker
    ) public returns (address) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

```
458:  function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol

```
21: function deploySyndicate(
        address _contractOwner,
        uint256 _priorityStakingEndBlock,
        address[] calldata _priorityStakers,
        bytes[] calldata _blsPubKeysForSyndicateKnots
    ) public override returns (address) {
```


### (05) 2**(N) - 1 SHOULD BE RE-WRITTEN AS TYPE(UINT(N)).MAX

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
870: sETH.approve(syndicate, (2 ** 256) - 1);
```


### (06) ADDING A RETURN STATEMENT WHEN THE FUNCTION DEFINES A NAMED RETURN VARIABLE, IS REDUNDANT

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol


```
921: function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {
```

### (07) EVENTS MISSING INDEXED FIELDS

*Number of Instances Identified: 35*

Each `event` should use three `indexed` fields if there are three or more fields

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```
16: event ETHWithdrawnByDepositor(address depositor, uint256 amount);
19: event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
22: event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
25: event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol

```
13: event ETHDeposited(address indexed sender, uint256 amount); 
16: event LPBurnedForETH(address indexed sender, uint256 amount); 
19: event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```
16: event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);
```


https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
36: event WhitelistingStatusChanged(address indexed dao, bool updatedStatus);
39: event NodeRunnerWhitelistingStatusChanged(address indexed nodeRunner, bool updatedStatus);
48: event WalletCredited(address indexed smartWallet, uint256 amount);
51: event KnotStaked(bytes _blsPublicKeyOfKnot, address indexed trigerringAddress);
54: event StakehouseCreated(string stakehouseTicker, address indexed stakehouse);
57: event StakehouseJoined(bytes blsPubKey);
63: event DormantRepresentative(address indexed associatedSmartWallet, address representative);
66: event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);
69: event NetworkTickerUpdated(string newTicker);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol

```
19: event DETHRedeemed(address depositor, uint256 amount);
22:  event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
121: event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol

```
25: event ETHDeposited(address sender, uint256 amount);
28: event ETHWithdrawn(address receiver, address admin, uint256 amount);
31: event ERC20Recovered(address admin, address recipient, uint256 amount);
34: event WETHUnwrapped(address admin, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```
9: event ETHReceived(uint256 amount);
12: event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

```
42: event UpdateAccruedETH(uint256 unprocessed);
45: event CollateralizedSLOTReCalibrated(bytes BLSPubKey);
48: event KNOTRegistered(bytes BLSPubKey);
51: event KnotDeRegistered(bytes BLSPubKey);
57: event Staked(bytes BLSPubKey, uint256 amount);
60: event UnStaked(bytes BLSPubKey, uint256 amount);
63: event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);
```


### (08) CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

*Number of Instances Identified: 5*

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```
88: require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");
89: require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");
112: require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");
```


https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
662: require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
663: require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
```


### (09) OPEN TODO

*Number of Instances Identified: 1*

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

```
195:  // todo - check else case for any ETH lost
```


### (10) NATSPEC IS INCOMPLETE

`@params` missing.
This is applicable to all the contracts, some of the observed instances are as follows:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol

```
41: constructor(...)
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol

```
14: constructor(address _manager)
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol

```
34: struct KnotDETHDetails{...}
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol

```
32: function createWallet(address owner) external returns (address wallet)
```


### (11) FLOATING PRAGMA VERSION SHOULD NOT BE USED

This is applicable to all the contracts

```
pragma solidity ^0.8.13;
```

### (12) PREFER USING LATEST SOLIDITY VERSION

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17
This is applicable to all the contracts.



## 01 EVENT IS MISSING INDEXED FIELDS

Each `event` should use three `indexed` fields if there are three or more fields

_There are 35 instances of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```
File: contracts/liquid-staking/ETHPoolLPFactory.sol

16: event ETHWithdrawnByDepositor(address depositor, uint256 amount);
19: event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
22: event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
25: event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol

```
File: contracts/liquid-staking/GiantPoolBase.sol

13: event ETHDeposited(address indexed sender, uint256 amount);
16: event LPBurnedForETH(address indexed sender, uint256 amount);
19: event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

16: event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

36: event WhitelistingStatusChanged(address indexed dao, bool updatedStatus);
39: event NodeRunnerWhitelistingStatusChanged(address indexed nodeRunner, bool updatedStatus);
48: event WalletCredited(address indexed smartWallet, uint256 amount);
51: event KnotStaked(bytes _blsPublicKeyOfKnot, address indexed trigerringAddress);
54: event StakehouseCreated(string stakehouseTicker, address indexed stakehouse);
57: event StakehouseJoined(bytes blsPubKey);
63: event DormantRepresentative(address indexed associatedSmartWallet, address representative);
66: event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);
69: event NetworkTickerUpdated(string newTicker);
84: event DAOCommissionUpdated(uint256 old, uint256 newCommission);
87: event NewLSDValidatorRegistered(address indexed nodeRunner, bytes blsPublicKey);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol

```
File: contracts/liquid-staking/SavETHVault.sol

19: event DETHRedeemed(address depositor, uint256 amount);
22: event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
121: event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol

```
File: contracts/liquid-staking/StakingFundsVault.sol

25: event ETHDeposited(address sender, uint256 amount);
28: event ETHWithdrawn(address receiver, address admin, uint256 amount);
31: event ERC20Recovered(address admin, address recipient, uint256 amount);
34: event WETHUnwrapped(address admin, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

9: event ETHReceived(uint256 amount);
12: event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

```
File: contracts/syndicate/Syndicate.sol

42: event UpdateAccruedETH(uint256 unprocessed);
45: event CollateralizedSLOTReCalibrated(bytes BLSPubKey);
48: event KNOTRegistered(bytes BLSPubKey);
51: event KnotDeRegistered(bytes BLSPubKey);
57: event Staked(bytes BLSPubKey, uint256 amount);
60: event UnStaked(bytes BLSPubKey, uint256 amount);
63: event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);
```

------------

## 02 OPEN TODOS

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

```
File: contracts/syndicate/Syndicate.sol

195: // todo - check else case for any ETH lost
```

----------

## 03 RACE CONDITION IN approve()

### Proof of Concept

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

870: sETH.approve(syndicate, (2 ** 256) - 1);
```

### Recommended Mitigation Steps

Add safeincreaseAllowance and safedecreaseAllowance methods in ERC20 contract

-----------

## 04 MISSING EVENTS FOR ONLY FUNCTIONS THAT CHANGE CRITICAL PARAMETERS

The functions that change critical parameters should emit events.

### Proof of Concept

_There are 3 instances of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

202: function executeAsSmartWallet(
218: function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {
226: function restoreFreeFloatingSharesToSmartWalletForRageQuit(
```

### Recommended Mitigation Steps

Add events to all functions that change critical parameters.

----------------

## 05 UPGRADEABLE CONTRACT IS MISSING A __GAP[50] STORAGE VARIABLE TO ALLOW FOR NEW STORAGE VARIABLES IN LATER VERSIONS

### Proof of Concept

_There is 1 instance of this issue_

See [this]([https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol

```
File: contracts/liquid-staking/LPToken.sol

11: contract LPToken is ILPTokenInit, ILiquidStakingManagerChildContract, Initializable, ERC20PermitUpgradeable {
```

----------

## 06 PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

### Proof of Concept

_There are 4 instances of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
Filw: contracts/liquid-staking/LiquidStakingManager.sol

514: function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol

```
File: contracts/syndicate/SyndicateFactory.sol

21: function deploySyndicate(
        address _contractOwner,
        uint256 _priorityStakingEndBlock,
        address[] calldata _priorityStakers,
        bytes[] calldata _blsPubKeysForSyndicateKnots
    ) public override returns (address) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol

```
File: contracts/liquid-staking/LSDNFactory.sol

73: function deployNewLiquidStakingDerivativeNetwork( 
        address _dao,
        uint256 _optionalCommission,
        bool _deployOptionalHouseGatekeeper,
        string calldata _stakehouseTicker
    ) public returns (address) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

```
File: contracts/syndicate/Syndicate.sol

458: function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {
```

---------
  
## 07 CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

### Proof of Concept

_There are 5 instances of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

662: require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
663: require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```
File: contracts/liquid-staking/ETHPoolLPFactory.sol

88: require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");
89: require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");
112: require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");
```

---------

## 08 NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

This issue exists in all the In-scope contracts

### Recommended Mitigation Steps

Lock the pragma version

--------------

## 09 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

921: function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {
```

----------

## 10 USE A MORE RECENT VERSION OF SOLIDITY

It's a best practice to use the latest compiler version.

The specified minimum compiler version is quite old. Older compilers might be susceptible to some bugs. We recommend changing the solidity version pragma to the latest version to enforce the use of an up to date compiler.

List of known compiler bugs and their severity can be found here: [https://etherscan.io/solcbuginfo](https://etherscan.io/solcbuginfo)

### Proof of Concept

This issue exists in all the In-scope contracts

--------------

## 11 NATSPEC IS INCOMPLETE

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

_There are several instances of this issue throughout the in scope contracts_

For example: 

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol

```
File: contracts/liquid-staking/GiantPoolBase.sol

92-93: /// @dev Check the msg.sender has enough giant LP to burn and that the pool has enough savETH vault LP
    function _assertUserHasEnoughGiantLPToClaimVaultLP(LPToken _token, uint256 _amount) internal view {
```

Missing: `@param _token`, `@param _amount`

---------


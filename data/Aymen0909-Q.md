# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1      | Front-runable `initialize` functions | Low | 5 |
| 2      | Related data should be grouped in a `struct` | NC |  9 |
| 3      | `2**<N> - 1` should be re-written as `type(uint<N>).max`  | NC | 1 |
| 4      | `constant` should be defined rather than using magic numbers  | NC | 10 |


## Findings

### 1- Front-runable `initialize` functions :

The `initialize` function is used in many contracts to initialize important contract parameters, and in all of them there is the issue that any attacker can initialize the contract before the legitimate deployer and even if the developers when deploying call immediately the `initialize` function, malicious agents can trace the protocol deployment transactions and insert their own transaction between them and by doing so they front run the developers call and gain the ownership of the contract and set the wrong parameters.

The impact of this issue is : 

* In the best case developers notice the problem and have to redeploy the contract and thus costing more gas.

* In the worst case the protocol continue to work with the wrong owner and state parameters which could lead to the loss of user funds.

#### Risk : Low 

#### Proof of Concept

Instances include:

File: contracts/liquid-staking/LiquidStakingManager.sol [Line 169](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L169)

```
function init(
    address _dao,
    address _syndicateFactory,
    address _smartWalletFactory,
    address _lpTokenFactory,
    address _brand,
    address _savETHVaultDeployer,
    address _stakingFundsVaultDeployer,
    address _optionalGatekeeperDeployer,
    uint256 _optionalCommission,
    bool _deployOptionalGatekeeper,
    string calldata _stakehouseTicker
) external virtual override initializer
```

File: contracts/liquid-staking/LPToken.sol [Line 32](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L32)

```
function init(
    address _deployer,
    address _transferHookProcessor,
    string calldata _tokenSymbol,
    string calldata _tokenName
) external override initializer
```

File: contracts/liquid-staking/SavETHVault.sol [Line 45](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L45)

```
function init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) external virtual initializer
```

File: contracts/liquid-staking/StakingFundsVault.sol [Line 46](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L46)

```
function init(address _liquidStakingNetworkManager, LPTokenFactory _lpTokenFactory) external virtual initializer
```

File: contracts/syndicate/Syndicate.sol [Line 129-134](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L129-L134)

```
function initialize(
    address _contractOwner,
    uint256 _priorityStakingEndBlock,
    address[] memory _priorityStakers,
    bytes[] memory _blsPubKeysForSyndicateKnots
) external virtual override initializer
```

#### Mitigation

It's recommended to use the constructor to initialize non-proxied contracts.

For initializing proxy (upgradable) contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify that the transaction succeeded.

### 2- Related data should be grouped in a struct :

When there are mappings that use the same key value, having separate fields is error prone, for instance in case of deletion or with future new fields.

#### Impact - NON CRITICAL

#### Proof of Concept

Instances include:

```
File: contracts/syndicate/Syndicate.sol

90       mapping(bytes => bool) public isKnotRegistered;
99       mapping(bytes => uint256) public sETHTotalStakeForKnot;
102      mapping(bytes => mapping(address => uint256)) public sETHStakedBalanceForKnot;
105      mapping(bytes => mapping(address => uint256)) public sETHUserClaimForKnot;
108      mapping(bytes => uint256) public totalETHProcessedPerCollateralizedKnot;
111      mapping(bytes => mapping(address => uint256)) public accruedEarningPerCollateralizedSlotOwnerOfKnot;
114      mapping(bytes => mapping(address => uint256)) public claimedPerCollateralizedSlotOwnerOfKnot;
117      mapping(bytes => bool) public isNoLongerPartOfSyndicate;
120      mapping(bytes => uint256) public lastAccumulatedETHPerFreeFloatingShare;
```
#### Mitigation

Those mappings should be refactored into the following `struct` and `mapping` for example :

```
struct Knot {
    /// @notice Informational - is the knot registered to this syndicate or not - the node should point to this contract
    bool isKnotRegistered;

    /// @notice Total amount of free floating sETH staked
    uint256 sETHTotalStakeForKnot;

    /// @notice Amount of sETH staked by user against a knot
    mapping(address => uint256) sETHStakedBalanceForKnot;

    /// @notice Amount of ETH claimed by user from sETH staking
    mapping(address => uint256) sETHUserClaimForKnot;

    /// @notice Total amount of ETH that has been allocated to the collateralized SLOT owners of a KNOT
    uint256 totalETHProcessedPerCollateralizedKnot;

    /// @notice Total amount of ETH accrued for the collateralized SLOT owner of a KNOT
    mapping(address => uint256) accruedEarningPerCollateralizedSlotOwnerOfKnot;

    /// @notice Total amount of ETH claimed by the collateralized SLOT owner of a KNOT
    mapping(address => uint256) claimedPerCollateralizedSlotOwnerOfKnot;

    /// @notice Whether a BLS public key, that has been previously registered, is no longer part of the syndicate and its shares (free floating or SLOT) cannot earn any more rewards
    bool isNoLongerPartOfSyndicate;

    /// @notice Once a BLS public key is no longer part of the syndicate, the accumulated ETH per free floating SLOT share is snapshotted so historical earnings can be drawn down correctly
    uint256 lastAccumulatedETHPerFreeFloatingShare;
}
    
mapping(address => Knot) public knotDetails;
```

### 3- `2**<N> - 1` should be re-written as `type(uint<N>).max` :

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/liquid-staking/LiquidStakingManager.sol

[(2 ** 256) - 1](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L870)

#### Mitigation
Replace the aforementioned statements for better readability.

### 4- `constant` should be defined rather than using magic numbers :

It is best practice to use constant variables rather than hex/numeric literal values to make the code easier to understand and maintain, but if they are used those numbers should be well docummented. 

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

```
File: contracts/liquid-staking/ETHPoolLPFactory.sol

82      require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
83      require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");
88      require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");
89      require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");
117     require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");

File: contracts/liquid-staking/GiantPoolBase.sol

96      require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");

File: contracts/liquid-staking/GiantMevAndFeesPool.sol

116     require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

File: contracts/liquid-staking/GiantSavETHVaultPool.sol

127     require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

File: contracts/liquid-staking/StakingFundsVault.sol

184     require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");
230     require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
```

#### Mitigation
Replace the hex/numeric literals aforementioned with `constants`.


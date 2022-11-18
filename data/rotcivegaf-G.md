
# Gas report

## Author: rotcivegaf

| G-N    |Issue|Instances|
|:------:|:----|:-------:|
| [G&#x2011;01] | Don't compare boolean expressions to boolean literals | 18 |
| [G&#x2011;02] | Use Custom Errors | 21 |
| [G&#x2011;03] | Struct packing | 1 |
| [G&#x2011;04] | Unused mapping | 1 |
| [G&#x2011;05] | Using `private` rather than `public`, saves gas | 7 |
| [G&#x2011;06] | Use `immutable` | 15 |
| [G&#x2011;07] | Use parameter function, don't save in a new variable | 1 |

## [G-01] Don't compare boolean expressions to boolean literals

`require(<x> == true)` => `require(<x>)`
`require(<x> == false)` => `require(!<x>)`

```solidity
File: contracts/syndicate/Syndicate.sol

611        if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();

612        if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

 79            require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

114        require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

204            require(
205                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
206                "Unknown BLS public key"
207            );
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol

64            require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

84        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

291        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

328        require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

332        require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

393            require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

436        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

437        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

469            require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

541            require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

589            require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

688            require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
```

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

149                require(
150                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
151                    "ETH is either staked or derivatives minted"
152                );
```

## [G-02] Use Custom Errors

[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/) Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

91        require(
92            getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfPreviousKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
93            "Lifecycle status must be one"
94        );

96        require(
97            getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
98            "Lifecycle status must be one"
99        );
```

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

43            require(
44                liquidStakingDerivativeFactory.isLiquidStakingManager(address(sfv.liquidStakingNetworkManager())),
45                "Invalid liquid staking manager"
46            );
```

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

 49            require(
 50                liquidStakingDerivativeFactory.isLiquidStakingManager(address(savETHPool.liquidStakingManager())),
 51                "Invalid liquid staking manager"
 52            );

149                require(
150                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
151                    "ETH is either staked or derivatives minted"
152                );
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

334        require(
335            getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
336            "Initials not registered"
337        );

471            require(
472                getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKey) == IDataStructures.LifecycleStatus.UNBEGUN,
473                "Lifecycle status must be zero"
474            );

545            require(
546                getAccountManager().blsPublicKeyToLifecycleStatus(blsPubKey) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
547                "Initials not registered"
548            );

592            require(
593                getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.DEPOSIT_COMPLETED,
594                "Lifecycle status must be two"
595            );
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol

 65            require(
 66                getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
 67                "Lifecycle status must be one"
 68            );

 85         require(
 86            getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
 87            "Lifecycle status must be one"
 88        );

134        require(
135            validatorStatus == IDataStructures.LifecycleStatus.INITIALS_REGISTERED ||
136            validatorStatus == IDataStructures.LifecycleStatus.TOKENS_MINTED,
137            "Cannot burn LP tokens"
138        );
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

 80            require(
 81                getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
 82                "Lifecycle status must be one"
 83            );

115        require(
116            getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
117            "Lifecycle status must be one"
118        );

180        require(
181            getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
182            "Cannot burn LP tokens"
183        );

204            require(
205                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
206                "Unknown BLS public key"
207            );

210            require(
211                getAccountManager().blsPublicKeyToLifecycleStatus(_blsPubKeys[i]) == IDataStructures.LifecycleStatus.TOKENS_MINTED,
212                "Derivatives not minted"
213            );
```

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

 33        require(
 34            initialOwner != address(0),
 35            "OwnableSmartWallet: Attempting to initialize with zero address owner"
 36        );

100        require(
101            isTransferApproved(owner(), msg.sender),
102            "OwnableSmartWallet: Transfer is not allowed"
103        ); // F: [OSW-4]

115        require(
116            to != address(0),
117            "OwnableSmartWallet: Approval cannot be set for zero address"
118        ); // F: [OSW-2A]
```

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

37        require(owner != address(0), 'Wallet cannot be address 0');
```

## [G-03] Struct packing

If change the `savETHBalance` from `uint256` to `uint248`, the struct reduce the gas cost from 2 slots to 1 slot

```solidity
File: contracts/liquid-staking/SavETHVault.sol

From:
34    struct KnotDETHDetails {
35        uint256 savETHBalance;
36        bool withdrawn;
37    }
to:
34    struct KnotDETHDetails {
35        uint248 savETHBalance;
36        bool withdrawn;
37    }
```

## [G-04] Unused mapping

The `walletExists` mapping it's not used on-chain
If this mapping will be used off-chain you can read the `WalletCreated` and build this information off-chain
If this is the case, I suggest removing this mapping

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

20    mapping(address => bool) public walletExists;

41        walletExists[wallet] = true;
```

## [G-05] Using `private` rather than `public`, saves gas

```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol

12    ILiquidStakingManager public liquidStakingManager;
```

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

13    address implementation;
```

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

13    address implementation;
```

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

14    address public immutable masterWallet;
```

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

15    address public lpTokenImplementation;
```

```solidity
File: contracts/syndicate/SyndicateFactory.sol

13    address public syndicateImplementation;
```

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

15    address public liquidStakingManagerImplementation;
```


## [G-06] Use `immutable`

Marking these as immutable (as they never change outside the constructor) would avoid them taking space in the storage:

```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol

12    ILiquidStakingManager public liquidStakingManager;
```

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

13    address implementation;
```

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

13    address implementation;
```

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

15    address public lpTokenImplementation;
```

```solidity
File: contracts/syndicate/SyndicateFactory.sol

13    address public syndicateImplementation;
```

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

15    address public liquidStakingManagerImplementation;

18    address public syndicateFactory;

21    address public lpTokenFactory;

24    address public smartWalletFactory;

27    address public brand;

30    address public savETHVaultDeployer;

33    address public stakingFundsVaultDeployer;

36    address public optionalGatekeeperDeployer;
```

```solidity
File: contracts/liquid-staking/GiantLP.sol

11    address public pool;

14    ITransferHookProcessor public transferHookProcessor;
```

## [G-07] Use parameter function, don't save in a new variable

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

58        uint256 balance = _balance;
```

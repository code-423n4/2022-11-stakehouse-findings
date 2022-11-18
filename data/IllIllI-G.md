
## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 1 | - |
| [G&#x2011;02] | State variables only set in the constructor should be declared `immutable` | 33 | 31201 |
| [G&#x2011;03] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 8 | 960 |
| [G&#x2011;04] | State variables should be cached in stack variables rather than re-reading them from storage | 16 | 1552 |
| [G&#x2011;05] | The result of function calls should be cached rather than re-calling the function | 1 | - |
| [G&#x2011;06] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 16 | 1808 |
| [G&#x2011;07] | `internal` functions only called once can be inlined to save gas | 9 | 180 |
| [G&#x2011;08] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 38 | 2280 |
| [G&#x2011;09] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 39 | - |
| [G&#x2011;10] | Optimize names to save gas | 16 | 352 |
| [G&#x2011;11] | `internal` functions not called by the contract should be removed to save deployment gas | 2 | - |
| [G&#x2011;12] | Don't compare boolean expressions to boolean literals | 19 | 171 |
| [G&#x2011;13] | Ternary unnecessary | 1 | - |
| [G&#x2011;14] | Division by two should use bit shifting | 1 | 20 |
| [G&#x2011;15] | Stack variable used as a cheaper cache for a state variable is only used once | 1 | 3 |
| [G&#x2011;16] | Empty blocks should be removed or emit something | 1 | - |
| [G&#x2011;17] | Use custom errors rather than `revert()`/`require()` strings to save gas | 21 | - |
| [G&#x2011;18] | Functions guaranteed to revert when called by normal users can be marked `payable` | 20 | 420 |

Total: 243 instances over 18 issues with **38947 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.


## Gas Optimizations

### [G&#x2011;01]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There is 1 instance of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

134       /// @notice Node runner issued to Smart wallet. Smart wallet address <> Node runner address
135       mapping(address => address) public nodeRunnerOfSmartWallet;
136   
137       /// @notice Track number of staked KNOTs of a smart wallet
138:      mapping(address => uint256) public stakedKnotsOfSmartWallet;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L134-L138

### [G&#x2011;02]  State variables only set in the constructor should be declared `immutable`
Avoids a Gsset (**20000 gas**) in the constructor, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmacces - **100 gas**) with a `PUSH32` (**3 gas**). 

While `string`s are not value types, and therefore cannot be `immutable`/`constant` if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract `abstract` with `virtual` functions for the `string` accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

*There are 33 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantLP.sol

/// @audit pool (constructor)
25:           pool = _pool;

/// @audit pool (access)
30:           require(msg.sender == pool, "Only pool");

/// @audit pool (access)
35:           require(msg.sender == pool, "Only pool");

/// @audit transferHookProcessor (constructor)
26:           transferHookProcessor = ITransferHookProcessor(_transferHookProcessor);

/// @audit transferHookProcessor (access)
/// @audit transferHookProcessor (access)
40:           if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).beforeTokenTransfer(_from, _to, _amount);

/// @audit transferHookProcessor (access)
/// @audit transferHookProcessor (access)
46:           if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).afterTokenTransfer(_from, _to, _amount);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantLP.sol#L25

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

/// @audit lpTokenImplementation (constructor)
21:           lpTokenImplementation = _lpTokenImplementation;

/// @audit lpTokenImplementation (access)
37:           address newInstance = Clones.clone(lpTokenImplementation);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPTokenFactory.sol#L21

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

/// @audit liquidStakingManagerImplementation (constructor)
60:           liquidStakingManagerImplementation = _liquidStakingManagerImplementation;

/// @audit liquidStakingManagerImplementation (access)
81:           address newInstance = Clones.clone(liquidStakingManagerImplementation);

/// @audit syndicateFactory (constructor)
61:           syndicateFactory = _syndicateFactory;

/// @audit syndicateFactory (access)
84:               syndicateFactory,

/// @audit smartWalletFactory (constructor)
63:           smartWalletFactory = _smartWalletFactory;

/// @audit smartWalletFactory (access)
85:               smartWalletFactory,

/// @audit brand (constructor)
64:           brand = _brand;

/// @audit brand (access)
87:               brand,

/// @audit savETHVaultDeployer (constructor)
65:           savETHVaultDeployer = _savETHVaultDeployer;

/// @audit savETHVaultDeployer (access)
88:               savETHVaultDeployer,

/// @audit stakingFundsVaultDeployer (constructor)
66:           stakingFundsVaultDeployer = _stakingFundsVaultDeployer;

/// @audit stakingFundsVaultDeployer (access)
89:               stakingFundsVaultDeployer,

/// @audit optionalGatekeeperDeployer (constructor)
67:           optionalGatekeeperDeployer = _optionalGatekeeperDeployer;

/// @audit optionalGatekeeperDeployer (access)
90:               optionalGatekeeperDeployer,

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LSDNFactory.sol#L60

```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol

/// @audit liquidStakingManager (constructor)
15:           liquidStakingManager = ILiquidStakingManager(_manager);

/// @audit liquidStakingManager (access)
20:           return liquidStakingManager.isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L15

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

/// @audit implementation (constructor)
15:           implementation = address(new SavETHVault());

/// @audit implementation (access)
19:           address newVault = Clones.clone(implementation);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVaultDeployer.sol#L15

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

/// @audit implementation (constructor)
15:           implementation = address(new StakingFundsVault());

/// @audit implementation (access)
19:           address newVault = Clones.clone(implementation);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L15

```solidity
File: contracts/syndicate/SyndicateFactory.sol

/// @audit syndicateImplementation (constructor)
17:           syndicateImplementation = _syndicateImpl;

/// @audit syndicateImplementation (access)
29:               syndicateImplementation,

/// @audit syndicateImplementation (access)
54:           return Clones.predictDeterministicAddress(syndicateImplementation, salt);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/SyndicateFactory.sol#L17

```diff
diff --git a/contracts/liquid-staking/GiantLP.sol b/contracts/liquid-staking/GiantLP.sol
index e6026f9..ba475cd 100644
--- a/contracts/liquid-staking/GiantLP.sol
+++ b/contracts/liquid-staking/GiantLP.sol
@@ -8,10 +8,10 @@ import { ITransferHookProcessor } from "../interfaces/ITransferHookProcessor.sol
 contract GiantLP is ERC20 {
 
     /// @notice Address of giant pool that deployed the giant LP token
-    address public pool;
+    address public immutable pool;
 
     /// @notice Optional address of contract that will process transfers of giant LP
-    ITransferHookProcessor public transferHookProcessor;
+    ITransferHookProcessor public immutable transferHookProcessor;
 
     /// @notice Last interacted timestamp for a given address
     mapping(address => uint256) public lastInteractedTimestamp;
@@ -45,4 +45,4 @@ contract GiantLP is ERC20 {
         lastInteractedTimestamp[_to] = block.timestamp;
         if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).afterTokenTransfer(_from, _to, _amount);
     }
-}
\ No newline at end of file
+}
diff --git a/contracts/liquid-staking/LPTokenFactory.sol b/contracts/liquid-staking/LPTokenFactory.sol
index 06d32b0..b810954 100644
--- a/contracts/liquid-staking/LPTokenFactory.sol
+++ b/contracts/liquid-staking/LPTokenFactory.sol
@@ -12,7 +12,7 @@ contract LPTokenFactory {
     event LPTokenDeployed(address indexed factoryCloneToken);
 
     /// @notice Address of LP token implementation that is cloned on each LP token
-    address public lpTokenImplementation;
+    address public immutable lpTokenImplementation;
 
     /// @param _lpTokenImplementation Address of LP token implementation that is cloned on each LP token deployment
     constructor(address _lpTokenImplementation) {
@@ -46,4 +46,4 @@ contract LPTokenFactory {
 
         return newInstance;
     }
-}
\ No newline at end of file
+}
diff --git a/contracts/liquid-staking/LSDNFactory.sol b/contracts/liquid-staking/LSDNFactory.sol
index 7007e1a..afd569d 100644
--- a/contracts/liquid-staking/LSDNFactory.sol
+++ b/contracts/liquid-staking/LSDNFactory.sol
@@ -12,28 +12,28 @@ contract LSDNFactory {
     event LSDNDeployed(address indexed LiquidStakingManager);
 
     /// @notice Address of the liquid staking manager implementation that is cloned on each deployment
-    address public liquidStakingManagerImplementation;
+    address public immutable liquidStakingManagerImplementation;
 
     /// @notice Address of the factory that will deploy a syndicate for the network after the first knot is created
-    address public syndicateFactory;
+    address public syndicateFactory; // left this one since it requires large test changes
 
     /// @notice Address of the factory for deploying LP tokens in exchange for ETH supplied to stake a KNOT
     address public lpTokenFactory;
 
     /// @notice Address of the factory for deploying smart wallets used by node runners during staking
-    address public smartWalletFactory;
+    address public immutable smartWalletFactory;
 
     /// @notice Address of brand NFT
     address public brand;
 
     /// @notice Address of the contract that can deploy new instances of SavETHVault
-    address public savETHVaultDeployer;
+    address public immutable savETHVaultDeployer;
 
     /// @notice Address of the contract that can deploy new instances of StakingFundsVault
-    address public stakingFundsVaultDeployer;
+    address public immutable stakingFundsVaultDeployer;
 
     /// @notice Address of the contract that can deploy new instances of optional gatekeepers for controlling which knots can join the LSDN house
-    address public optionalGatekeeperDeployer;
+    address public immutable optionalGatekeeperDeployer;
 
     /// @notice Establishes whether a given liquid staking manager address was deployed by this factory
     mapping(address => bool) public isLiquidStakingManager;
@@ -100,4 +100,4 @@ contract LSDNFactory {
 
         return newInstance;
     }
-}
\ No newline at end of file
+}
diff --git a/contracts/liquid-staking/OptionalHouseGatekeeper.sol b/contracts/liquid-staking/OptionalHouseGatekeeper.sol
index fabedeb..96d49f4 100644
--- a/contracts/liquid-staking/OptionalHouseGatekeeper.sol
+++ b/contracts/liquid-staking/OptionalHouseGatekeeper.sol
@@ -9,7 +9,7 @@ import { ILiquidStakingManager } from "../interfaces/ILiquidStakingManager.sol";
 contract OptionalHouseGatekeeper is IGateKeeper {
 
     /// @notice Address of the core registry for the associated liquid staking network
-    ILiquidStakingManager public liquidStakingManager;
+    ILiquidStakingManager public immutable liquidStakingManager;
 
     constructor(address _manager) {
         liquidStakingManager = ILiquidStakingManager(_manager);
@@ -19,4 +19,4 @@ contract OptionalHouseGatekeeper is IGateKeeper {
     function isMemberPermitted(bytes calldata _blsPublicKeyOfKnot) external override view returns (bool) {
         return liquidStakingManager.isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot);
     }
-}
\ No newline at end of file
+}
diff --git a/contracts/liquid-staking/SavETHVaultDeployer.sol b/contracts/liquid-staking/SavETHVaultDeployer.sol
index 59e897e..f17a9ef 100644
--- a/contracts/liquid-staking/SavETHVaultDeployer.sol
+++ b/contracts/liquid-staking/SavETHVaultDeployer.sol
@@ -10,7 +10,7 @@ contract SavETHVaultDeployer {
 
     event NewVaultDeployed(address indexed instance);
 
-    address implementation;
+    address immutable implementation;
     constructor() {
         implementation = address(new SavETHVault());
     }
@@ -23,4 +23,4 @@ contract SavETHVaultDeployer {
 
         return newVault;
     }
-}
\ No newline at end of file
+}
diff --git a/contracts/liquid-staking/StakingFundsVaultDeployer.sol b/contracts/liquid-staking/StakingFundsVaultDeployer.sol
index 628e1bd..3e7fee2 100644
--- a/contracts/liquid-staking/StakingFundsVaultDeployer.sol
+++ b/contracts/liquid-staking/StakingFundsVaultDeployer.sol
@@ -10,7 +10,7 @@ contract StakingFundsVaultDeployer {
 
     event NewVaultDeployed(address indexed instance);
 
-    address implementation;
+    address immutable implementation;
     constructor() {
         implementation = address(new StakingFundsVault());
     }
@@ -23,4 +23,4 @@ contract StakingFundsVaultDeployer {
 
         return newVault;
     }
-}
\ No newline at end of file
+}
diff --git a/contracts/syndicate/SyndicateFactory.sol b/contracts/syndicate/SyndicateFactory.sol
index a405392..cf6ffb4 100644
--- a/contracts/syndicate/SyndicateFactory.sol
+++ b/contracts/syndicate/SyndicateFactory.sol
@@ -10,7 +10,7 @@ import { ISyndicateInit } from "../interfaces/ISyndicateInit.sol";
 contract SyndicateFactory is ISyndicateFactory {
 
     /// @notice Address of syndicate implementation that is cloned on each syndicate deployment
-    address public syndicateImplementation;
+    address public immutable syndicateImplementation;
 
     /// @param _syndicateImpl Address of syndicate implementation that is cloned on each syndicate deployment
     constructor(address _syndicateImpl) {
@@ -62,4 +62,4 @@ contract SyndicateFactory is ISyndicateFactory {
     ) public override pure returns (bytes32) {
         return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));
     }
-}
\ No newline at end of file
+}
```

Note that the numbers below are an underreporting of the gas changes due to [this](https://gist.github.com/0xA5DF/cc71e507d45fd51d708b3e8318654ce5) `forge` issue

```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index fba0986..4737598 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -6 +6 @@
-│ 783840                                                ┆ 4497            ┆        ┆        ┆        ┆         │
+│ 782932                                                ┆ 4746            ┆        ┆        ┆        ┆         │
@@ -12 +12 @@
-│ burn                                                  ┆ 3112            ┆ 3112   ┆ 3112   ┆ 3112   ┆ 1       │
+│ burn                                                  ┆ 2872            ┆ 2872   ┆ 2872   ┆ 2872   ┆ 1       │
@@ -16 +16 @@
-│ mint                                                  ┆ 95651           ┆ 100798 ┆ 100798 ┆ 105946 ┆ 2       │
+│ mint                                                  ┆ 91351           ┆ 96392  ┆ 96392  ┆ 101434 ┆ 2       │
@@ -25 +25 @@
-│ 2818760                                                                       ┆ 14268           ┆        ┆        ┆        ┆         │
+│ 2817876                                                                       ┆ 14517           ┆        ┆        ┆        ┆         │
@@ -31 +31 @@
-│ batchDepositETHForStaking                                                     ┆ 464810          ┆ 464810 ┆ 464810 ┆ 464810 ┆ 1       │
+│ batchDepositETHForStaking                                                     ┆ 464689          ┆ 464689 ┆ 464689 ┆ 464689 ┆ 1       │
@@ -35 +35 @@
-│ depositETH                                                                    ┆ 136571          ┆ 136571 ┆ 136571 ┆ 136571 ┆ 1       │
+│ depositETH                                                                    ┆ 132059          ┆ 132059 ┆ 132059 ┆ 132059 ┆ 1       │
@@ -71 +71 @@
-│ 244047                                                              ┆ 1376            ┆        ┆        ┆        ┆         │
+│ 230141                                                              ┆ 1415            ┆        ┆        ┆        ┆         │
@@ -75 +75 @@
-│ deployLPToken                                                       ┆ 1003            ┆ 203531 ┆ 210012 ┆ 229912 ┆ 51      │
+│ deployLPToken                                                       ┆ 1003            ┆ 202593 ┆ 207891 ┆ 227791 ┆ 51      │
@@ -77 +77 @@
-│ lpTokenImplementation                                               ┆ 2325            ┆ 2325   ┆ 2325   ┆ 2325   ┆ 1       │
+│ lpTokenImplementation                                               ┆ 204             ┆ 204    ┆ 204    ┆ 204    ┆ 1       │
@@ -84 +84 @@
-│ 201045                                                                                    ┆ 1036            ┆        ┆        ┆        ┆         │
+│ 210451                                                                                    ┆ 1083            ┆        ┆        ┆        ┆         │
@@ -88 +88 @@
-│ deploy                                                                                    ┆ 159472          ┆ 159472 ┆ 159472 ┆ 159472 ┆ 8       │
+│ deploy                                                                                    ┆ 147378          ┆ 147378 ┆ 147378 ┆ 147378 ┆ 8       │
@@ -148 +148 @@
-│ 2401073                                                                                         ┆ 12256           ┆        ┆        ┆        ┆         │
+│ 2420089                                                                                         ┆ 12505           ┆        ┆        ┆        ┆         │
@@ -152 +152 @@
-│ batchDepositETHForStaking                                                                       ┆ 442116          ┆ 442116 ┆ 442116 ┆ 442116 ┆ 1       │
+│ batchDepositETHForStaking                                                                       ┆ 439995          ┆ 439995 ┆ 439995 ┆ 439995 ┆ 1       │
@@ -154 +154 @@
-│ depositETH                                                                                      ┆ 124557          ┆ 124557 ┆ 124557 ┆ 124557 ┆ 1       │
+│ depositETH                                                                                      ┆ 120257          ┆ 120257 ┆ 120257 ┆ 120257 ┆ 1       │
@@ -158 +158 @@
-│ withdrawDETH                                                                                    ┆ 119578          ┆ 119578 ┆ 119578 ┆ 119578 ┆ 1       │
+│ withdrawDETH                                                                                    ┆ 119338          ┆ 119338 ┆ 119338 ┆ 119338 ┆ 1       │
@@ -210 +185 @@
-│ getNetworkFeeRecipient                                                                          ┆ 8652            ┆ 8652    ┆ 8652    ┆ 8652    ┆ 1       │
+│ getNetworkFeeRecipient                                                                          ┆ 6528            ┆ 6528    ┆ 6528    ┆ 6528    ┆ 1       │
@@ -212 +187 @@
-│ init                                                                                            ┆ 7845306         ┆ 7876536 ┆ 7845306 ┆ 8027382 ┆ 46      │
+│ init                                                                                            ┆ 7845306         ┆ 7874433 ┆ 7845306 ┆ 8015288 ┆ 46      │
@@ -218 +193 @@
-│ mintDerivatives                                                                                 ┆ 165229          ┆ 1169725 ┆ 1295056 ┆ 1296030 ┆ 9       │
+│ mintDerivatives                                                                                 ┆ 165229          ┆ 1168059 ┆ 1292932 ┆ 1293906 ┆ 9       │
@@ -277 +252 @@
-│ batchDepositETHForStaking                                                     ┆ 441100          ┆ 441100 ┆ 441100 ┆ 441100 ┆ 1       │
+│ batchDepositETHForStaking                                                     ┆ 438979          ┆ 438979 ┆ 438979 ┆ 438979 ┆ 1       │
@@ -289 +264 @@
-│ depositETHForStaking                                                          ┆ 6515            ┆ 340328 ┆ 439226 ┆ 441966 ┆ 22      │
+│ depositETHForStaking                                                          ┆ 6515            ┆ 339143 ┆ 437105 ┆ 439845 ┆ 22      │
@@ -318 +293 @@
-│ batchDepositETHForStaking                                                                 ┆ 960             ┆ 474698 ┆ 472169 ┆ 1362765 ┆ 14      │
+│ batchDepositETHForStaking                                                                 ┆ 960             ┆ 473577 ┆ 471048 ┆ 1360402 ┆ 14      │
@@ -332 +307 @@
-│ depositETHForStaking                                                                      ┆ 6730            ┆ 257794 ┆ 428647 ┆ 487587  ┆ 13      │
+│ depositETHForStaking                                                                      ┆ 6730            ┆ 257575 ┆ 428526 ┆ 485466  ┆ 13      │
@@ -492 +467 @@
-│ 3494833                                                                            ┆ 17331           ┆        ┆        ┆        ┆         │
+│ 3484573                                                                            ┆ 17408           ┆        ┆        ┆        ┆         │
@@ -496 +471 @@
-│ calculateSyndicateDeploymentAddress                                                ┆ 3207            ┆ 3207   ┆ 3207   ┆ 3207   ┆ 1       │
+│ calculateSyndicateDeploymentAddress                                                ┆ 1083            ┆ 1083   ┆ 1083   ┆ 1083   ┆ 1       │
@@ -498 +473 @@
-│ deployMockSyndicate                                                                ┆ 183696          ┆ 183696 ┆ 183696 ┆ 183696 ┆ 7       │
+│ deployMockSyndicate                                                                ┆ 183572          ┆ 183572 ┆ 183572 ┆ 183572 ┆ 7       │
@@ -500 +475 @@
-│ deploySyndicate                                                                    ┆ 181812          ┆ 182057 ┆ 182092 ┆ 182092 ┆ 8       │
+│ deploySyndicate                                                                    ┆ 179968          ┆ 180183 ┆ 179968 ┆ 181688 ┆ 8       │
@@ -582,0 +558 @@
+Overall gas change: -97881 (-27.471%)
```


### [G&#x2011;03]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 8 instances of this issue:*
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

/// @audit _blsPubKeys
355:      function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L355

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

/// @audit callData
41        function execute(address target, bytes memory callData)
42            external
43            override
44            payable
45            onlyOwner // F: [OSW-6A]
46:           returns (bytes memory)

/// @audit callData
52        function execute(
53            address target,
54            bytes memory callData,
55            uint256 value
56        )
57            external
58            override
59            payable
60            onlyOwner // F: [OSW-6A]
61:           returns (bytes memory)

/// @audit callData
67        function rawExecute(
68            address target,
69            bytes memory callData,
70            uint256 value
71        )
72        external
73        override
74        payable
75        onlyOwner
76:       returns (bytes memory)

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWallet.sol#L41-L46

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit _priorityStakers
/// @audit _blsPubKeysForSyndicateKnots
129       function initialize(
130           address _contractOwner,
131           uint256 _priorityStakingEndBlock,
132           address[] memory _priorityStakers,
133           bytes[] memory _blsPubKeysForSyndicateKnots
134:      ) external virtual override initializer {

/// @audit _blsPubKey
337:      function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {

/// @audit _blsPubKeys
343:      function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L129-L134

### [G&#x2011;04]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 16 instances of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

/// @audit numberOfLPTokensIssued on line 131
141:              numberOfLPTokensIssued++;

/// @audit lpTokenFactory on line 137
138:                               LPToken(lpTokenFactory.deployLPToken(address(this), address(0), tokenSymbol, tokenName));

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L141

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit dao on line 241
243:          emit UpdateDAOAddress(dao, _newAddress);

/// @audit stakehouseTicker on line 822
866:          emit StakehouseCreated(stakehouseTicker, stakehouse);

/// @audit stakehouse on line 833
834:          IERC20 sETH = IERC20(getSlotRegistry().stakeHouseShareTokens(stakehouse));

/// @audit stakehouse on line 834
838:              stakehouse,

/// @audit stakehouse on line 838
846:              IStakeHouseRegistry(stakehouse).setGateKeeper(address(gatekeeper));

/// @audit stakehouse on line 846
866:          emit StakehouseCreated(stakehouseTicker, stakehouse);

/// @audit brand on line 780
788:                  IBrandNFT(brand).lowercaseBrandTickerToTokenId(lowerTicker),

/// @audit syndicate on line 853
861:          sETH.approve(syndicate, (2 ** 256) - 1);

/// @audit enableWhitelisting on line 269
270:          emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);

/// @audit enableWhitelisting on line 270
272:          return enableWhitelisting;

/// @audit daoCommissionPercentage on line 915
916:              uint256 daoAmount = (_received * daoCommissionPercentage) / MODULO;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L243

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit accumulatedETHPerCollateralizedSlotPerKnot on line 494
527:                  totalETHProcessedPerCollateralizedKnot[_blsPubKey] = accumulatedETHPerCollateralizedSlotPerKnot;

/// @audit totalFreeFloatingShares on line 551
551:          return totalFreeFloatingShares > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingShares : 0;

/// @audit numberOfRegisteredKnots on line 177
189:              uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L527



```diff
diff --git a/contracts/liquid-staking/ETHPoolLPFactory.sol b/contracts/liquid-staking/ETHPoolLPFactory.sol
index 4bd43b2..6207f41 100644
--- a/contracts/liquid-staking/ETHPoolLPFactory.sol
+++ b/contracts/liquid-staking/ETHPoolLPFactory.sol
@@ -128,17 +128,16 @@ abstract contract ETHPoolLPFactory is StakehouseAPI {
         else {
             // mint new LP tokens for the new KNOT
             // add the KNOT in the mapping
-            string memory tokenNumber = Strings.toString(numberOfLPTokensIssued);
+            // and increase the count of LP tokens
+            string memory tokenNumber = Strings.toString(numberOfLPTokensIssued++);
             string memory tokenName = string(abi.encodePacked(baseLPTokenName, tokenNumber));
             string memory tokenSymbol = string(abi.encodePacked(baseLPTokenSymbol, tokenNumber));
 
             // deploy new LP token and optionally enable transfer notifications
+            LPTokenFactory lpTokenFactoryCache = lpTokenFactory;
             LPToken newLPToken = _enableTransferHook ?
-                             LPToken(lpTokenFactory.deployLPToken(address(this), address(this), tokenSymbol, tokenName)) :
-                             LPToken(lpTokenFactory.deployLPToken(address(this), address(0), tokenSymbol, tokenName));
-
-            // increase the count of LP tokens
-            numberOfLPTokensIssued++;
+                             LPToken(lpTokenFactoryCache.deployLPToken(address(this), address(this), tokenSymbol, tokenName)) :
+                             LPToken(lpTokenFactoryCache.deployLPToken(address(this), address(0), tokenSymbol, tokenName));
 
             // register the BLS Public Key with the LP token
             lpTokenForKnot[_blsPublicKeyOfKnot] = newLPToken;
@@ -149,4 +148,4 @@ abstract contract ETHPoolLPFactory is StakehouseAPI {
             emit NewLPTokenIssued(_blsPublicKeyOfKnot, address(newLPToken), msg.sender, _amount);
         }
     }
-}
\ No newline at end of file
+}
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 5fe8c49..a939b64 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -238,9 +238,10 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
     /// @notice Allow DAO to migrate to a new address
     function updateDAOAddress(address _newAddress) external onlyDAO {
         require(_newAddress != address(0), "Zero address");
-        require(_newAddress != dao, "Same address");
+        address daoCache = dao;
+        require(_newAddress != daoCache, "Same address");
 
-        emit UpdateDAOAddress(dao, _newAddress);
+        emit UpdateDAOAddress(daoCache, _newAddress);
 
         dao = _newAddress;
     }
@@ -266,10 +267,9 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
     /// @param _changeWhitelist boolean value. true to enable and false to disable
     function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {
         require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
-        enableWhitelisting = _changeWhitelist;
-        emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);
+        emit WhitelistingStatusChanged(msg.sender, enableWhitelisting = _changeWhitelist);
 
-        return enableWhitelisting;
+        return _changeWhitelist;
     }
 
     /// @notice function to enable/disable whitelisting of a noderunner
@@ -777,7 +777,8 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         address associatedSmartWallet = smartWalletOfKnot[_blsPubKey];
 
         // Join the LSDN stakehouse
-        string memory lowerTicker = IBrandNFT(brand).toLowerCase(stakehouseTicker);
+        address brandCache = brand;
+        string memory lowerTicker = IBrandNFT(brandCache).toLowerCase(stakehouseTicker);
         IOwnableSmartWallet(associatedSmartWallet).execute(
             address(getTransactionRouter()),
             abi.encodeWithSelector(
@@ -785,7 +786,7 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
                 associatedSmartWallet,
                 _blsPubKey,
                 stakehouse,
-                IBrandNFT(brand).lowercaseBrandTickerToTokenId(lowerTicker),
+                IBrandNFT(brandCache).lowercaseBrandTickerToTokenId(lowerTicker),
                 savETHVault.indexOwnedByTheVault(),
                 _beaconChainBalanceReport,
                 _reportSignature
@@ -813,13 +814,14 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         // The savETH will go to the savETH vault, the collateralized SLOT for syndication owned by the smart wallet
         // sETH will also be minted in the smart wallet but will be moved out and distributed to the syndicate for claiming by the DAO
         address associatedSmartWallet = smartWalletOfKnot[_blsPublicKeyOfKnot];
+        string memory stakehouseTickerCache = stakehouseTicker;
         IOwnableSmartWallet(associatedSmartWallet).execute(
             address(getTransactionRouter()),
             abi.encodeWithSelector(
                 ITransactionRouter.createStakehouse.selector,
                 associatedSmartWallet,
                 _blsPublicKeyOfKnot,
-                stakehouseTicker,
+                stakehouseTickerCache,
                 savETHVault.indexOwnedByTheVault(),
                 _beaconChainBalanceReport,
                 _reportSignature
@@ -830,12 +832,13 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         numberOfKnots += 1;
 
         // Capture the address of the Stakehouse for future knots to join
-        stakehouse = getStakeHouseUniverse().memberKnotToStakeHouse(_blsPublicKeyOfKnot);
-        IERC20 sETH = IERC20(getSlotRegistry().stakeHouseShareTokens(stakehouse));
+        address stakehouseCache = 
+          stakehouse = getStakeHouseUniverse().memberKnotToStakeHouse(_blsPublicKeyOfKnot);
+        IERC20 sETH = IERC20(getSlotRegistry().stakeHouseShareTokens(stakehouseCache));
 
         // Give liquid staking manager ability to manage keepers and set a house keeper if decided by the network
         IOwnableSmartWallet(associatedSmartWallet).execute(
-            stakehouse,
+            stakehouseCache,
             abi.encodeWithSelector(
                 Ownable.transferOwnership.selector,
                 address(this)
@@ -843,14 +846,14 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         );
 
         if (address(gatekeeper) != address(0)) {
-            IStakeHouseRegistry(stakehouse).setGateKeeper(address(gatekeeper));
+            IStakeHouseRegistry(stakehouseCache).setGateKeeper(address(gatekeeper));
         }
 
         // Deploy the EIP1559 transaction reward sharing contract but no priority required because sETH will be auto staked
         address[] memory priorityStakers = new address[](0);
         bytes[] memory initialKnots = new bytes[](1);
         initialKnots[0] = _blsPublicKeyOfKnot;
-        syndicate = syndicateFactory.deploySyndicate(
+        address syndicateCache = syndicate = syndicateFactory.deploySyndicate(
             address(this),
             0,
             priorityStakers,
@@ -858,12 +861,12 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         );
 
         // Contract approves syndicate to take sETH on behalf of the DAO
-        sETH.approve(syndicate, (2 ** 256) - 1);
+        sETH.approve(syndicateCache, (2 ** 256) - 1);
 
         // Auto-stake sETH by pulling sETH out the smart wallet and staking in the syndicate
         _autoStakeWithSyndicate(associatedSmartWallet, _blsPublicKeyOfKnot);
 
-        emit StakehouseCreated(stakehouseTicker, stakehouse);
+        emit StakehouseCreated(stakehouseTickerCache, stakehouseCache);
     }
 
     /// @dev Remove the sETH from the node runner smart wallet in order to auto-stake the sETH in the syndicate
@@ -912,8 +915,9 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
     function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {
         require(_received > 0, "Nothing received");
 
-        if (daoCommissionPercentage > 0) {
-            uint256 daoAmount = (_received * daoCommissionPercentage) / MODULO;
+        uint256 daoCommissionPercentageCache = daoCommissionPercentage;
+        if (daoCommissionPercentageCache > 0) {
+            uint256 daoAmount = (_received * daoCommissionPercentageCache) / MODULO;
             uint256 rest = _received - daoAmount;
             return (rest, daoAmount);
         }
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..7dff745 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -174,7 +174,8 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
     function updateAccruedETHPerShares() public {
         // Ensure there are registered KNOTs. Syndicates are deployed with at least 1 registered but this can fall to zero.
         // Fee recipient should be re-assigned in the event that happens as any further ETH can be collected by owner
-        if (numberOfRegisteredKnots > 0) {
+        uint256 numberOfRegisteredKnotsCache = numberOfRegisteredKnots;
+        if (numberOfRegisteredKnotsCache > 0) {
             // All time, total ETH that was earned per slot type (free floating or collateralized)
             uint256 totalEthPerSlotType = calculateETHForFreeFloatingOrCollateralizedHolders();
 
@@ -186,7 +187,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
                 lastSeenETHPerFreeFloating = totalEthPerSlotType;
             }
 
-            uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);
+            uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnotsCache);
             accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
             lastSeenETHPerCollateralizedSlotPerKnot = totalEthPerSlotType;
 
@@ -490,8 +491,9 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
     /// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this amongs the current set of collateralized owners (a dynamic set of addresses and balances)
     function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {
         // Establish how much new ETH is for the new KNOT
+        uint256 accumulatedETHPerCollateralizedSlotPerKnotCache = accumulatedETHPerCollateralizedSlotPerKnot;
         uint256 unprocessedETHForCurrentKnot =
-                    accumulatedETHPerCollateralizedSlotPerKnot - totalETHProcessedPerCollateralizedKnot[_blsPubKey];
+                    accumulatedETHPerCollateralizedSlotPerKnotCache - totalETHProcessedPerCollateralizedKnot[_blsPubKey];
 
         // Get information about the knot i.e. associated house and whether its active
         (address stakeHouse,,,,,bool isActive) = getStakeHouseUniverse().stakeHouseKnotInfo(_blsPubKey);
@@ -524,7 +526,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
                 }
 
                 // record so unprocessed goes to zero
-                totalETHProcessedPerCollateralizedKnot[_blsPubKey] = accumulatedETHPerCollateralizedSlotPerKnot;
+                totalETHProcessedPerCollateralizedKnot[_blsPubKey] = accumulatedETHPerCollateralizedSlotPerKnotCache;
             }
         }
 
@@ -548,7 +550,8 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
 
     /// @dev Business logic for calculating per free floating share how much ETH from 1559 rewards is owed
     function _calculateNewAccumulatedETHPerFreeFloatingShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) {
-        return totalFreeFloatingShares > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingShares : 0;
+        uint256 totalFreeFloatingSharesCache = totalFreeFloatingShares;
+        return totalFreeFloatingSharesCache > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingSharesCache : 0;
     }
 
     /// @dev Business logic for adding a new set of knots to the syndicate for collecting revenue
@@ -678,4 +681,4 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
             }
         }
     }
-}
\ No newline at end of file
+}
```

Note that the numbers below are an underreporting of the gas changes due to [this](https://gist.github.com/0xA5DF/cc71e507d45fd51d708b3e8318654ce5) `forge` issue

```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index fba0986..97bb2fa 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -31 +31 @@
-│ batchDepositETHForStaking                                                     ┆ 464810          ┆ 464810 ┆ 464810 ┆ 464810 ┆ 1       │
+│ batchDepositETHForStaking                                                     ┆ 464737          ┆ 464737 ┆ 464737 ┆ 464737 ┆ 1       │
@@ -64 +64 @@
-│ transfer                                              ┆ 125385          ┆ 125385 ┆ 125385 ┆ 125385 ┆ 1       │
+│ transfer                                              ┆ 125191          ┆ 125191 ┆ 125191 ┆ 125191 ┆ 1       │
@@ -99 +99 @@
-│ execute(address,bytes)(bytes)                                             ┆ 2508            ┆ 110200 ┆ 3537   ┆ 846684 ┆ 68      │
+│ execute(address,bytes)(bytes)                                             ┆ 2508            ┆ 110188 ┆ 3537   ┆ 846684 ┆ 68      │
@@ -129 +129 @@
-│ approve(address,uint256)                           ┆ 24624           ┆ 24624 ┆ 24624  ┆ 24624 ┆ 1       │
+│ approve(address,uint256)                           ┆ 24624           ┆ 24624 ┆ 24624  ┆ 24624 ┆ 2       │
@@ -131 +131 @@
-│ approve(address,uint256)(bool)                     ┆ 22524           ┆ 24507 ┆ 24624  ┆ 24624 ┆ 18      │
+│ approve(address,uint256)(bool)                     ┆ 22524           ┆ 24500 ┆ 24624  ┆ 24624 ┆ 17      │
@@ -139 +139 @@
-│ transferFrom(address,address,uint256)              ┆ 20508           ┆ 20508 ┆ 20508  ┆ 20508 ┆ 1       │
+│ transferFrom(address,address,uint256)              ┆ 20508           ┆ 20508 ┆ 20508  ┆ 20508 ┆ 2       │
@@ -141 +141 @@
-│ transferFrom(address,address,uint256)(bool)        ┆ 2988            ┆ 16645 ┆ 20508  ┆ 26127 ┆ 19      │
+│ transferFrom(address,address,uint256)(bool)        ┆ 2988            ┆ 16430 ┆ 20508  ┆ 26127 ┆ 18      │
@@ -152 +152 @@
-│ batchDepositETHForStaking                                                                       ┆ 442116          ┆ 442116 ┆ 442116 ┆ 442116 ┆ 1       │
+│ batchDepositETHForStaking                                                                       ┆ 442043          ┆ 442043 ┆ 442043 ┆ 442043 ┆ 1       │
@@ -165 +165 @@
-│ 7497782                                                                       ┆ 37351           ┆         ┆         ┆         ┆         │
+│ 7496782                                                                       ┆ 37346           ┆         ┆         ┆         ┆         │
@@ -173 +173 @@
-│ deployNewMockLiquidStakingDerivativeNetwork                                   ┆ 7912949         ┆ 7944581 ┆ 7912949 ┆ 8095025 ┆ 46      │
+│ deployNewMockLiquidStakingDerivativeNetwork                                   ┆ 7914560         ┆ 7946192 ┆ 7914560 ┆ 8096636 ┆ 46      │
@@ -190 +190 @@
-│ 12369773                                                                                        ┆ 61714           ┆         ┆         ┆         ┆         │
+│ 12402840                                                                                        ┆ 61879           ┆         ┆         ┆         ┆         │
@@ -196 +196 @@
-│ claimRewardsAsNodeRunner                                                                        ┆ 21013           ┆ 118803  ┆ 110983  ┆ 232235  ┆ 4       │
+│ claimRewardsAsNodeRunner                                                                        ┆ 20830           ┆ 118604  ┆ 110858  ┆ 231871  ┆ 4       │
@@ -200 +200 @@
-│ deRegisterKnotFromSyndicate                                                                     ┆ 151263          ┆ 151263  ┆ 151263  ┆ 151263  ┆ 1       │
+│ deRegisterKnotFromSyndicate                                                                     ┆ 151036          ┆ 151036  ┆ 151036  ┆ 151036  ┆ 1       │
@@ -212 +212 @@
-│ init                                                                                            ┆ 7845306         ┆ 7876536 ┆ 7845306 ┆ 8027382 ┆ 46      │
+│ init                                                                                            ┆ 7846917         ┆ 7878147 ┆ 7846917 ┆ 8028993 ┆ 46      │
@@ -218 +218 @@
-│ mintDerivatives                                                                                 ┆ 165229          ┆ 1169725 ┆ 1295056 ┆ 1296030 ┆ 9       │
+│ mintDerivatives                                                                                 ┆ 164815          ┆ 1169899 ┆ 1295366 ┆ 1296240 ┆ 9       │
@@ -224 +224 @@
-│ restoreFreeFloatingSharesToSmartWalletForRageQuit                                               ┆ 123847          ┆ 123847  ┆ 123847  ┆ 123847  ┆ 1       │
+│ restoreFreeFloatingSharesToSmartWalletForRageQuit                                               ┆ 123857          ┆ 123857  ┆ 123857  ┆ 123857  ┆ 1       │
@@ -258 +258 @@
-│ updateDAOAddress                                                                                ┆ 2592            ┆ 3992    ┆ 3992    ┆ 5392    ┆ 2       │
+│ updateDAOAddress                                                                                ┆ 2515            ┆ 3915    ┆ 3915    ┆ 5315    ┆ 2       │
@@ -260 +260 @@
-│ updateWhitelisting                                                                              ┆ 5455            ┆ 5455    ┆ 5455    ┆ 5455    ┆ 1       │
+│ updateWhitelisting                                                                              ┆ 5321            ┆ 5321    ┆ 5321    ┆ 5321    ┆ 1       │
@@ -269 +269 @@
-│ 3049390                                                                       ┆ 15294           ┆        ┆        ┆        ┆         │
+│ 3050198                                                                       ┆ 15298           ┆        ┆        ┆        ┆         │
@@ -277 +277 @@
-│ batchDepositETHForStaking                                                     ┆ 441100          ┆ 441100 ┆ 441100 ┆ 441100 ┆ 1       │
+│ batchDepositETHForStaking                                                     ┆ 441027          ┆ 441027 ┆ 441027 ┆ 441027 ┆ 1       │
@@ -289 +289 @@
-│ depositETHForStaking                                                          ┆ 6515            ┆ 340328 ┆ 439226 ┆ 441966 ┆ 22      │
+│ depositETHForStaking                                                          ┆ 6515            ┆ 340271 ┆ 439153 ┆ 441893 ┆ 22      │
@@ -312 +312 @@
-│ 3990984                                                                                   ┆ 19996           ┆        ┆        ┆         ┆         │
+│ 3991784                                                                                   ┆ 20000           ┆        ┆        ┆         ┆         │
@@ -318 +318 @@
-│ batchDepositETHForStaking                                                                 ┆ 960             ┆ 474698 ┆ 472169 ┆ 1362765 ┆ 14      │
+│ batchDepositETHForStaking                                                                 ┆ 960             ┆ 474625 ┆ 472096 ┆ 1362546 ┆ 14      │
@@ -320 +320 @@
-│ beforeTokenTransfer                                                                       ┆ 1813            ┆ 5228   ┆ 1813   ┆ 74855   ┆ 29      │
+│ beforeTokenTransfer                                                                       ┆ 1813            ┆ 5221   ┆ 1813   ┆ 74661   ┆ 29      │
@@ -328 +328 @@
-│ claimRewards                                                                              ┆ 55587           ┆ 172294 ┆ 168285 ┆ 351598  ┆ 8       │
+│ claimRewards                                                                              ┆ 55393           ┆ 172124 ┆ 168091 ┆ 351404  ┆ 8       │
@@ -332 +332 @@
-│ depositETHForStaking                                                                      ┆ 6730            ┆ 257794 ┆ 428647 ┆ 487587  ┆ 13      │
+│ depositETHForStaking                                                                      ┆ 6730            ┆ 257755 ┆ 428574 ┆ 487514  ┆ 13      │
@@ -344 +344 @@
-│ previewAccumulatedETH                                                                     ┆ 9704            ┆ 13421  ┆ 11032  ┆ 17032   ┆ 12      │
+│ previewAccumulatedETH                                                                     ┆ 9606            ┆ 13323  ┆ 10934  ┆ 16934   ┆ 12      │
@@ -352 +352 @@
-│ unstakeSyndicateSharesForRageQuit                                                         ┆ 121907          ┆ 121907 ┆ 121907 ┆ 121907  ┆ 1       │
+│ unstakeSyndicateSharesForRageQuit                                                         ┆ 121917          ┆ 121917 ┆ 121917 ┆ 121917  ┆ 1       │
@@ -492 +492 @@
-│ 3494833                                                                            ┆ 17331           ┆        ┆        ┆        ┆         │
+│ 3493833                                                                            ┆ 17326           ┆        ┆        ┆        ┆         │
@@ -511 +511 @@
-│ 2990771                                                              ┆ 15093           ┆        ┆        ┆        ┆         │
+│ 2989771                                                              ┆ 15088           ┆        ┆        ┆        ┆         │
@@ -525 +525 @@
-│ calculateNewAccumulatedETHPerFreeFloatingShare                       ┆ 1199            ┆ 1199   ┆ 1199   ┆ 1199   ┆ 1       │
+│ calculateNewAccumulatedETHPerFreeFloatingShare                       ┆ 1101            ┆ 1101   ┆ 1101   ┆ 1101   ┆ 1       │
@@ -527 +527 @@
-│ claimAsCollateralizedSLOTOwner                                       ┆ 10767           ┆ 114826 ┆ 114333 ┆ 245733 ┆ 13      │
+│ claimAsCollateralizedSLOTOwner                                       ┆ 10584           ┆ 114589 ┆ 114050 ┆ 245450 ┆ 13      │
@@ -529 +529 @@
-│ claimAsStaker                                                        ┆ 8220            ┆ 61175  ┆ 47989  ┆ 202304 ┆ 25      │
+│ claimAsStaker                                                        ┆ 8026            ┆ 60989  ┆ 47795  ┆ 202110 ┆ 25      │
@@ -531 +531 @@
-│ deRegisterKnots                                                      ┆ 148072          ┆ 148072 ┆ 148072 ┆ 148072 ┆ 1       │
+│ deRegisterKnots                                                      ┆ 147845          ┆ 147845 ┆ 147845 ┆ 147845 ┆ 1       │
@@ -553 +553 @@
-│ previewUnclaimedETHAsFreeFloatingStaker                              ┆ 2806            ┆ 3351   ┆ 2806   ┆ 4806   ┆ 22      │
+│ previewUnclaimedETHAsFreeFloatingStaker                              ┆ 2708            ┆ 3253   ┆ 2708   ┆ 4708   ┆ 22      │
@@ -555 +555 @@
-│ registerKnotsToSyndicate                                             ┆ 6503            ┆ 57756  ┆ 64257  ┆ 108857 ┆ 5       │
+│ registerKnotsToSyndicate                                             ┆ 6309            ┆ 57582  ┆ 64063  ┆ 108663 ┆ 5       │
@@ -565 +565 @@
-│ stake                                                                ┆ 37539           ┆ 89145  ┆ 99152  ┆ 160976 ┆ 21      │
+│ stake                                                                ┆ 37384           ┆ 89027  ┆ 99056  ┆ 160880 ┆ 21      │
@@ -579 +579 @@
-│ unstake                                                              ┆ 15073           ┆ 16506  ┆ 16506  ┆ 17939  ┆ 2       │
+│ unstake                                                              ┆ 15078           ┆ 16431  ┆ 16431  ┆ 17784  ┆ 2       │
@@ -581 +581 @@
-│ updateAccruedETHPerShares                                            ┆ 83678           ┆ 83678  ┆ 83678  ┆ 83678  ┆ 1       │
+│ updateAccruedETHPerShares                                            ┆ 83484           ┆ 83484  ┆ 83484  ┆ 83484  ┆ 1       │
@@ -582,0 +583 @@
+Overall gas change: -13020 (-1.742%)
```

### [G&#x2011;05]  The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function

*There is 1 instance of this issue:*
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

/// @audit liquidStakingNetworkManager.syndicate() on line 215
219:                      liquidStakingNetworkManager.syndicate(),

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L219

### [G&#x2011;06]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 16 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

39:           idleETH += msg.value;

57:           idleETH -= _amount;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L39

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

773:          numberOfKnots += 1;

830:          numberOfKnots += 1;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L773

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

58:           totalShares += 4 ether;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L58

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

65:                   totalClaimed += due;

85:                   accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L65

```solidity
File: contracts/syndicate/Syndicate.sol

185:                  accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);

190:              accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;

225:              totalFreeFloatingShares += _sETHAmount;

269:                  totalFreeFloatingShares -= _sETHAmount;

317:                  totalClaimed += unclaimedUserShare;

558:          numberOfRegisteredKnots += knotsToRegister;

621:          totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];

624:          numberOfRegisteredKnots -= 1;

658:                  totalClaimed += unclaimedUserShare;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L185

### [G&#x2011;07]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 9 instances of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

636       function _init(
637           address _dao,
638           address _syndicateFactory,
639           address _smartWalletFactory,
640           address _lpTokenFactory,
641           address _brand,
642           address _savETHVaultDeployer,
643           address _stakingFundsVaultDeployer,
644           address _optionalGatekeeperDeployer,
645           uint256 _optionalCommission,
646           bool _deployOptionalGatekeeper,
647:          string calldata _stakehouseTicker

675:      function _isNodeRunnerValid(address _nodeRunner) internal view returns (bool) {

730       function _stake(
731           bytes calldata _blsPublicKey,
732           bytes calldata _cipherText,
733           bytes calldata _aesEncryptorKey,
734           IDataStructures.EIP712Signature calldata _encryptionSignature,
735:          bytes32 dataRoot

767       function _joinLSDNStakehouse(
768           bytes calldata _blsPubKey,
769           IDataStructures.ETH2DataReport calldata _beaconChainBalanceReport,
770:          IDataStructures.EIP712Signature calldata _reportSignature

807       function _createLSDNStakehouse(
808           bytes calldata _blsPublicKeyOfKnot,
809           IDataStructures.ETH2DataReport calldata _beaconChainBalanceReport,
810:          IDataStructures.EIP712Signature calldata _reportSignature

925:      function _assertEtherIsReadyForValidatorStaking(bytes calldata blsPubKey) internal view {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L636-L647

```solidity
File: contracts/liquid-staking/SavETHVault.sol

235:      function _init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) internal {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L235

```solidity
File: contracts/syndicate/Syndicate.sol

469       function _initialize(
470           address _contractOwner,
471           uint256 _priorityStakingEndBlock,
472           address[] memory _priorityStakers,
473:          bytes[] memory _blsPubKeysForSyndicateKnots

597:      function _deRegisterKnots(bytes[] calldata _blsPublicKeys) internal {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L469-L473

### [G&#x2011;08]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 38 instances of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

63:           for (uint256 i; i < numOfRotations; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L63

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

38:           for (uint256 i; i < numOfVaults; ++i) {

64:           for (uint256 i; i < numOfVaults; ++i) {

90:           for (uint256 i; i < _stakingFundsVaults.length; ++i) {

117:          for (uint256 i; i < numOfRotations; ++i) {

135:          for (uint256 i; i < numOfVaults; ++i) {

183:          for (uint256 i; i < _lpTokens.length; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L38

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

76:           for (uint256 i; i < amountOfTokens; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L76

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

42:           for (uint256 i; i < numOfSavETHVaults; ++i) {

78:           for (uint256 i; i < numOfVaults; ++i) {

82:               for (uint256 j; j < _lpTokens[i].length; ++j) {

128:          for (uint256 i; i < numOfRotations; ++i) {

146:          for (uint256 i; i < numOfVaults; ++i) {

148:              for (uint256 j; j < _lpTokens[i].length; ++j) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L42

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

390:          for(uint256 i; i < _blsPubKeys.length; ++i) {

463:          for(uint256 i; i < len; ++i) {

529:          for (uint256 i; i < numOfValidators; ++i) {

578:          for (uint256 i; i < numOfKnotsToProcess; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L390

```solidity
File: contracts/liquid-staking/SavETHVault.sol

63:           for (uint256 i; i < numOfValidators; ++i) {

103:          for (uint256 i; i < numOfTokens; ++i) {

116:          for (uint256 i; i < numOfTokens; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L63

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

78:           for (uint256 i; i < numOfValidators; ++i) {

152:          for (uint256 i; i < numOfTokens; ++i) {

166:          for (uint256 i; i < numOfTokens; ++i) {

203:          for (uint256 i; i < _blsPubKeys.length; ++i) {

266:          for (uint256 i; i < _blsPublicKeys.length; ++i) {

276:          for (uint256 i; i < _blsPubKeys.length; ++i) {

286:          for (uint256 i; i < _token.length; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L78

```solidity
File: contracts/syndicate/Syndicate.sol

211:          for (uint256 i; i < _blsPubKeys.length; ++i) {

259:          for (uint256 i; i < _blsPubKeys.length; ++i) {

301:          for (uint256 i; i < _blsPubKeys.length; ++i) {

346:          for (uint256 i; i < _blsPubKeys.length; ++i) {

420:          for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

513:                      for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

560:          for (uint256 i; i < knotsToRegister; ++i) {

585:          for (uint256 i; i < _priorityStakers.length; ++i) {

598:          for (uint256 i; i < _blsPublicKeys.length; ++i) {

648:          for (uint256 i; i < _blsPubKeys.length; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L211

### [G&#x2011;09]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 39 instances of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

122:              require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L122

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

55:           require(idleETH >= _amount, "Come back later or withdraw less ETH");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L55

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

149                   require(
150                       vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
151                       "ETH is either staked or derivatives minted"
152:                  );

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L149-L152

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

256:          require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

257:          require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

258:          require(numberOfKnots == 0, "Cannot change ticker once house is created");

268:          require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");

280:          require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

291:          require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

328:          require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

331:          require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");

332:          require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

391:              require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

394:              require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

433:          require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");

435:          require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

453:              require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");

467:              require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

532:              require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

580:              require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

653:          require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");

654:          require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

927:          require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

930:          require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");

931:          require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

935:          require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L256

```solidity
File: contracts/liquid-staking/SavETHVault.sol

64:               require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

84:           require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

90:           require(msg.value == _amount, "Must provide correct amount of ETH");

204:          require(_amount >= 24 ether, "Amount cannot be less than 24 ether");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L64

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

79:               require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

114:          require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

120:          require(msg.value == _amount, "Must provide correct amount of ETH");

154:              require(address(token) != address(0), "No ETH staked for specified BLS key");

240:          require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

267:              require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L79

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

33            require(
34                initialOwner != address(0),
35                "OwnableSmartWallet: Attempting to initialize with zero address owner"
36:           );

100           require(
101               isTransferApproved(owner(), msg.sender),
102               "OwnableSmartWallet: Transfer is not allowed"
103:          ); // F: [OSW-4]

115           require(
116               to != address(0),
117               "OwnableSmartWallet: Approval cannot be set for zero address"
118:          ); // F: [OSW-2A]

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWallet.sol#L33-L36

### [G&#x2011;10]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 16 instances of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

/// @audit batchRotateLPTokens(), rotateLPTokens()
13:   abstract contract ETHPoolLPFactory is StakehouseAPI {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L13

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

/// @audit batchDepositETHForStaking(), claimRewards(), previewAccumulatedETH(), batchRotateLPTokens(), bringUnusedETHBackIntoGiantPool(), updateAccumulatedETHPerLP(), beforeTokenTransfer(), afterTokenTransfer()
15:   contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, SyndicateRewardsProcessor {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L15

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

/// @audit depositETH(), withdrawETH(), withdrawLPTokens()
10:   contract GiantPoolBase is ReentrancyGuard {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L10

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

/// @audit batchDepositETHForStaking(), withdrawDETH(), batchRotateLPTokens(), bringUnusedETHBackIntoGiantPool()
13:   contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L13

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit executeAsSmartWallet(), deRegisterKnotFromSyndicate(), restoreFreeFloatingSharesToSmartWalletForRageQuit(), updateDAOAddress(), updateDAORevenueCommission(), updateTicker(), updateWhitelisting(), updateNodeRunnerWhitelistStatus(), rotateEOARepresentative(), rotateEOARepresentativeOfNodeRunner(), withdrawETHForKnot(), rotateNodeRunnerOfSmartWallet(), claimRewardsAsNodeRunner(), registerBLSPublicKeys(), isBLSPublicKeyPartOfLSDNetwork(), isBLSPublicKeyBanned(), isNodeRunnerBanned(), stake(), mintDerivatives(), getNetworkFeeRecipient()
33:   contract LiquidStakingManager is ILiquidStakingManager, Initializable, ReentrancyGuard, StakehouseAPI {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L33

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

/// @audit deployLPToken()
9:    contract LPTokenFactory {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPTokenFactory.sol#L9

```solidity
File: contracts/liquid-staking/LPToken.sol

/// @audit liquidStakingManager()
11:   contract LPToken is ILPTokenInit, ILiquidStakingManagerChildContract, Initializable, ERC20PermitUpgradeable {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPToken.sol#L11

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

/// @audit deployNewLiquidStakingDerivativeNetwork()
9:    contract LSDNFactory {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LSDNFactory.sol#L9

```solidity
File: contracts/liquid-staking/OptionalGatekeeperFactory.sol

/// @audit deploy()
7:    contract OptionalGatekeeperFactory {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L7

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

/// @audit deploySavETHVault()
9:    contract SavETHVaultDeployer {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVaultDeployer.sol#L9

```solidity
File: contracts/liquid-staking/SavETHVault.sol

/// @audit init(), batchDepositETHForStaking(), depositETHForStaking(), burnLPTokensByBLS(), burnLPTokens(), burnLPToken(), withdrawETHForStaking(), isBLSPublicKeyPartOfLSDNetwork(), isBLSPublicKeyBanned(), isDETHReadyForWithdrawal()
16:   contract SavETHVault is Initializable, ETHPoolLPFactory, ReentrancyGuard {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L16

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

/// @audit deployStakingFundsVault()
9:    contract StakingFundsVaultDeployer {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L9

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

/// @audit init(), updateDerivativesMinted(), updateAccumulatedETHPerLP(), batchDepositETHForStaking(), depositETHForStaking(), burnLPTokensForETHByBLS(), burnLPTokensForETH(), burnLPForETH(), claimRewards(), withdrawETH(), unstakeSyndicateSharesForRageQuit(), batchPreviewAccumulatedETHByBLSKeys(), batchPreviewAccumulatedETH(), previewAccumulatedETH(), claimFundsFromSyndicateForDistribution()
20:   contract StakingFundsVault is

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L20

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

/// @audit totalRewardsReceived()
6:    abstract contract SyndicateRewardsProcessor {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L6

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

/// @audit createWallet(), createWallet()
11:   contract OwnableSmartWalletFactory is IOwnableSmartWalletFactory {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L11

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit registerKnotsToSyndicate(), deRegisterKnots(), addPriorityStakers(), updatePriorityStakingBlock(), updateAccruedETHPerShares(), stake(), unstake(), claimAsStaker(), claimAsCollateralizedSLOTOwner(), updateCollateralizedSlotOwnersAccruedETH(), batchUpdateCollateralizedSlotOwnersAccruedETH(), calculateUnclaimedFreeFloatingETHShare(), calculateETHForFreeFloatingOrCollateralizedHolders(), previewUnclaimedETHAsFreeFloatingStaker(), previewUnclaimedETHAsCollateralizedSlotOwner(), getUnprocessedETHForAllFreeFloatingSlot(), getUnprocessedETHForAllCollateralizedSlot(), calculateNewAccumulatedETHPerFreeFloatingShare(), calculateNewAccumulatedETHPerCollateralizedSharePerKnot(), totalETHReceived()
36:   contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, StakehouseAPI {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L36

### [G&#x2011;11]  `internal` functions not called by the contract should be removed to save deployment gas
If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

*There are 2 instances of this issue:*
```solidity
File: contracts/syndicate/Syndicate.sol

538:      function _calculateCollateralizedETHOwedPerKnot() internal view returns (uint256) {

545:      function _calculateNewAccumulatedETHPerCollateralizedShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L538

### [G&#x2011;12]  Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

*There are 19 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

150:                      vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L150

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

291:          require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

328:          require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

332:          require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

391:              require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

434:          require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

435:          require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

467:              require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

532:              require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

580:              require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

679:              require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L291

```solidity
File: contracts/liquid-staking/SavETHVault.sol

64:               require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

84:           require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L64

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

79:               require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

114:          require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

205:                  liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L79

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

145:          return from == to ? true : _isTransferApproved[from][to]; // F: [OSW-2, 3]

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWallet.sol#L145

```solidity
File: contracts/syndicate/Syndicate.sol

611:          if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();

612:          if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L611

### [G&#x2011;13]  Ternary unnecessary
`z = (x == y) ? true : false` => `z = (x == y)`

*There is 1 instance of this issue:*
```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

145:          return from == to ? true : _isTransferApproved[from][to]; // F: [OSW-2, 3]

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWallet.sol#L145

### [G&#x2011;14]  Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two

*There is 1 instance of this issue:*
```solidity
File: contracts/syndicate/Syndicate.sol

378:          return ethPerKnot / 2;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L378

### [G&#x2011;15]  Stack variable used as a cheaper cache for a state variable is only used once
If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the **3 gas** the extra stack assignment would spend

*There is 1 instance of this issue:*
```solidity
File: contracts/syndicate/Syndicate.sol

388:          uint256 currentAccumulatedETHPerFreeFloatingShare = accumulatedETHPerFreeFloatingShare;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L388

### [G&#x2011;16]  Empty blocks should be removed or emit something 
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`). Empty `receive()`/`fallback() payable` functions that are not used, can be removed to save deployment gas.

*There is 1 instance of this issue:*
```solidity
File: contracts/syndicate/Syndicate.sol

194           } else {
195               // todo - check else case for any ETH lost
196:          }

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L194-L196

### [G&#x2011;17]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 21 instances of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

91            require(
92                getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfPreviousKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
93                "Lifecycle status must be one"
94:           );

96            require(
97                getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
98                "Lifecycle status must be one"
99:           );

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L91-L94

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

43                require(
44                    liquidStakingDerivativeFactory.isLiquidStakingManager(address(sfv.liquidStakingNetworkManager())),
45                    "Invalid liquid staking manager"
46:               );

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L43-L46

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

49                require(
50                    liquidStakingDerivativeFactory.isLiquidStakingManager(address(savETHPool.liquidStakingManager())),
51                    "Invalid liquid staking manager"
52:               );

149                   require(
150                       vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
151                       "ETH is either staked or derivatives minted"
152:                  );

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L49-L52

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

334           require(
335               getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
336               "Initials not registered"
337:          );

469               require(
470                   getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKey) == IDataStructures.LifecycleStatus.UNBEGUN,
471                   "Lifecycle status must be zero"
472:              );

536               require(
537                   getAccountManager().blsPublicKeyToLifecycleStatus(blsPubKey) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
538                   "Initials not registered"
539:              );

583               require(
584                   getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.DEPOSIT_COMPLETED,
585                   "Lifecycle status must be two"
586:              );

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L334-L337

```solidity
File: contracts/liquid-staking/SavETHVault.sol

65                require(
66                    getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
67                    "Lifecycle status must be one"
68:               );

85            require(
86                getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
87                "Lifecycle status must be one"
88:           );

134           require(
135               validatorStatus == IDataStructures.LifecycleStatus.INITIALS_REGISTERED ||
136               validatorStatus == IDataStructures.LifecycleStatus.TOKENS_MINTED,
137               "Cannot burn LP tokens"
138:          );

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L65-L68

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

80                require(
81                    getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
82                    "Lifecycle status must be one"
83:               );

115           require(
116               getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
117               "Lifecycle status must be one"
118:          );

180           require(
181               getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
182               "Cannot burn LP tokens"
183:          );

204               require(
205                   liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
206                   "Unknown BLS public key"
207:              );

210               require(
211                   getAccountManager().blsPublicKeyToLifecycleStatus(_blsPubKeys[i]) == IDataStructures.LifecycleStatus.TOKENS_MINTED,
212                   "Derivatives not minted"
213:              );

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L80-L83

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

37:           require(owner != address(0), 'Wallet cannot be address 0');

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L37

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

33            require(
34                initialOwner != address(0),
35                "OwnableSmartWallet: Attempting to initialize with zero address owner"
36:           );

100           require(
101               isTransferApproved(owner(), msg.sender),
102               "OwnableSmartWallet: Transfer is not allowed"
103:          ); // F: [OSW-4]

115           require(
116               to != address(0),
117               "OwnableSmartWallet: Approval cannot be set for zero address"
118:          ); // F: [OSW-2A]

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWallet.sol#L33-L36

### [G&#x2011;18]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 20 instances of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

218:      function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {

226       function restoreFreeFloatingSharesToSmartWalletForRageQuit(
227           address _smartWallet,
228           bytes[] calldata _blsPublicKeys,
229           uint256[] calldata _amounts
230:      ) external onlyDAO {

239:      function updateDAOAddress(address _newAddress) external onlyDAO {

249:      function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {

255:      function updateTicker(string calldata _newTicker) external onlyDAO {

267:      function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {

278:      function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {

308:      function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO {

357:      function rotateNodeRunnerOfSmartWallet(address _current, address _new, bool _wasPreviousNodeRunnerMalicious) external onlyDAO {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L218

```solidity
File: contracts/liquid-staking/LPToken.sol

46:       function mint(address _recipient, uint256 _amount) external onlyDeployer {

51:       function burn(address _recipient, uint256 _amount) external onlyDeployer {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPToken.sol#L46

```solidity
File: contracts/liquid-staking/SavETHVault.sol

200       function withdrawETHForStaking(
201           address _smartWallet,
202           uint256 _amount
203:      ) public onlyManager nonReentrant returns (uint256) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L200-L203

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

56:       function updateDerivativesMinted() external onlyManager {

239:      function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {

255       function unstakeSyndicateSharesForRageQuit(
256           address _sETHRecipient,
257           bytes[] calldata _blsPublicKeys,
258           uint256[] calldata _amounts
259:      ) external onlyManager nonReentrant {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L56

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

114:      function setApproval(address to, bool status) external onlyOwner override {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWallet.sol#L114

```solidity
File: contracts/syndicate/Syndicate.sol

145       function registerKnotsToSyndicate(
146           bytes[] calldata _newBLSPublicKeyBeingRegistered
147:      ) external onlyOwner {

154:      function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {

161:      function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {

168:      function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L145-L147

___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `<array>.length` should not be looked up in every loop of a `for`-loop | 16 | 48 |
| [G&#x2011;02] | Using `bool`s for storage incurs overhead | 9 | 153900 |
| [G&#x2011;03] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 1 | 5 |
| [G&#x2011;04] | Using `private` rather than `public` for constants, saves gas | 4 | - |
| [G&#x2011;05] | Use custom errors rather than `revert()`/`require()` strings to save gas | 198 | - |

Total: 228 instances over 5 issues with **153953 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.



## Gas Optimizations

### [G&#x2011;01]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 16 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

/// @audit (valid but excluded finding)
90:           for (uint256 i; i < _stakingFundsVaults.length; ++i) {

/// @audit (valid but excluded finding)
183:          for (uint256 i; i < _lpTokens.length; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L90

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

/// @audit (valid but excluded finding)
82:               for (uint256 j; j < _lpTokens[i].length; ++j) {

/// @audit (valid but excluded finding)
148:              for (uint256 j; j < _lpTokens[i].length; ++j) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L82

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit (valid but excluded finding)
390:          for(uint256 i; i < _blsPubKeys.length; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L390

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

/// @audit (valid but excluded finding)
203:          for (uint256 i; i < _blsPubKeys.length; ++i) {

/// @audit (valid but excluded finding)
266:          for (uint256 i; i < _blsPublicKeys.length; ++i) {

/// @audit (valid but excluded finding)
276:          for (uint256 i; i < _blsPubKeys.length; ++i) {

/// @audit (valid but excluded finding)
286:          for (uint256 i; i < _token.length; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L203

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit (valid but excluded finding)
211:          for (uint256 i; i < _blsPubKeys.length; ++i) {

/// @audit (valid but excluded finding)
259:          for (uint256 i; i < _blsPubKeys.length; ++i) {

/// @audit (valid but excluded finding)
301:          for (uint256 i; i < _blsPubKeys.length; ++i) {

/// @audit (valid but excluded finding)
346:          for (uint256 i; i < _blsPubKeys.length; ++i) {

/// @audit (valid but excluded finding)
585:          for (uint256 i; i < _priorityStakers.length; ++i) {

/// @audit (valid but excluded finding)
598:          for (uint256 i; i < _blsPublicKeys.length; ++i) {

/// @audit (valid but excluded finding)
648:          for (uint256 i; i < _blsPubKeys.length; ++i) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L211

### [G&#x2011;02]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There are 9 instances of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit (valid but excluded finding)
120:      bool public enableWhitelisting;

/// @audit (valid but excluded finding)
123:      mapping(address => bool) public isNodeRunnerWhitelisted;

/// @audit (valid but excluded finding)
149:      mapping(address => bool) public bannedNodeRunners;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L120

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

/// @audit (valid but excluded finding)
39:       mapping(address => bool) public isLiquidStakingManager;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LSDNFactory.sol#L39

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

/// @audit (valid but excluded finding)
20:       mapping(address => bool) public walletExists;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L20

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

/// @audit (valid but excluded finding)
22:       mapping(address => mapping(address => bool)) internal _isTransferApproved;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWallet.sol#L22

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit (valid but excluded finding)
90:       mapping(bytes => bool) public isKnotRegistered;

/// @audit (valid but excluded finding)
96:       mapping(address => bool) public isPriorityStaker;

/// @audit (valid but excluded finding)
117:      mapping(bytes => bool) public isNoLongerPartOfSyndicate;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L90

### [G&#x2011;03]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There is 1 instance of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

/// @audit (valid but excluded finding)
141:              numberOfLPTokensIssued++;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L141

### [G&#x2011;04]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 4 instances of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

/// @audit (valid but excluded finding)
38:       uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L38

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

/// @audit (valid but excluded finding)
22:       uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L22

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

/// @audit (valid but excluded finding)
15:       uint256 public constant PRECISION = 1e24;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L15

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit (valid but excluded finding)
66:       uint256 public constant PRECISION = 1e24;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L66

### [G&#x2011;05]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 198 instances of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

/// @audit (valid but excluded finding)
59:           require(numOfRotations > 0, "Empty arrays");

/// @audit (valid but excluded finding)
60:           require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
61:           require(numOfRotations == _amounts.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
77:           require(address(_oldLPToken) != address(0), "Zero address");

/// @audit (valid but excluded finding)
78:           require(address(_newLPToken) != address(0), "Zero address");

/// @audit (valid but excluded finding)
79:           require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");

/// @audit (valid but excluded finding)
80:           require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

/// @audit (valid but excluded finding)
81:           require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");

/// @audit (valid but excluded finding)
82:           require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");

/// @audit (valid but excluded finding)
83:           require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

/// @audit (valid but excluded finding)
88:           require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");

/// @audit (valid but excluded finding)
89:           require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");

/// @audit (valid but excluded finding)
111:          require(_amount >= MIN_STAKING_AMOUNT, "Min amount not reached");

/// @audit (valid but excluded finding)
112:          require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");

/// @audit (valid but excluded finding)
122:              require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L59

```solidity
File: contracts/liquid-staking/GiantLP.sol

/// @audit (valid but excluded finding)
30:           require(msg.sender == pool, "Only pool");

/// @audit (valid but excluded finding)
35:           require(msg.sender == pool, "Only pool");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantLP.sol#L30

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

/// @audit (valid but excluded finding)
35:           require(numOfVaults > 0, "Zero vaults");

/// @audit (valid but excluded finding)
36:           require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");

/// @audit (valid but excluded finding)
37:           require(numOfVaults == _amounts.length, "Inconsistent lengths");

/// @audit (valid but excluded finding)
62:           require(numOfVaults > 0, "Empty array");

/// @audit (valid but excluded finding)
63:           require(numOfVaults == _blsPublicKeysForKnots.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
87:           require(_stakingFundsVaults.length == _lpTokens.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
112:          require(numOfRotations > 0, "Empty arrays");

/// @audit (valid but excluded finding)
113:          require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
114:          require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
115:          require(numOfRotations == _amounts.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
116:          require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

/// @audit (valid but excluded finding)
132:          require(numOfVaults > 0, "Empty arrays");

/// @audit (valid but excluded finding)
133:          require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
134:          require(numOfVaults == _amounts.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
147:          require(msg.sender == address(lpTokenETH), "Caller is not giant LP");

/// @audit (valid but excluded finding)
171:          require(msg.sender == address(lpTokenETH), "Caller is not giant LP");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L35

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

/// @audit (valid but excluded finding)
35:           require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");

/// @audit (valid but excluded finding)
36:           require(msg.value == _amount, "Value equal to amount");

/// @audit (valid but excluded finding)
53:           require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

/// @audit (valid but excluded finding)
54:           require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");

/// @audit (valid but excluded finding)
55:           require(idleETH >= _amount, "Come back later or withdraw less ETH");

/// @audit (valid but excluded finding)
61:           require(success, "Failed to transfer ETH");

/// @audit (valid but excluded finding)
71:           require(amountOfTokens > 0, "Empty arrays");

/// @audit (valid but excluded finding)
72:           require(amountOfTokens == _amounts.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
94:           require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

/// @audit (valid but excluded finding)
95:           require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");

/// @audit (valid but excluded finding)
96:           require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L35

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

/// @audit (valid but excluded finding)
36:           require(numOfSavETHVaults > 0, "Empty arrays");

/// @audit (valid but excluded finding)
37:           require(numOfSavETHVaults == _ETHTransactionAmounts.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
38:           require(numOfSavETHVaults == _blsPublicKeys.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
39:           require(numOfSavETHVaults == _stakeAmounts.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
72:           require(numOfVaults > 0, "Empty arrays");

/// @audit (valid but excluded finding)
73:           require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
74:           require(numOfVaults == _amounts.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
89:                   require(vault.isDETHReadyForWithdrawal(address(token)), "dETH is not ready for withdrawal");

/// @audit (valid but excluded finding)
92:                   require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");

/// @audit (valid but excluded finding)
123:          require(numOfRotations > 0, "Empty arrays");

/// @audit (valid but excluded finding)
124:          require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
125:          require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
126:          require(numOfRotations == _amounts.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
127:          require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

/// @audit (valid but excluded finding)
143:          require(numOfVaults > 0, "Empty arrays");

/// @audit (valid but excluded finding)
144:          require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

/// @audit (valid but excluded finding)
145:          require(numOfVaults == _amounts.length, "Inconsistent arrays");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L36

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit (valid but excluded finding)
161:          require(msg.sender == dao, "Must be DAO");

/// @audit (valid but excluded finding)
209:          require(smartWallet != address(0), "No wallet found");

/// @audit (valid but excluded finding)
240:          require(_newAddress != address(0), "Zero address");

/// @audit (valid but excluded finding)
241:          require(_newAddress != dao, "Same address");

/// @audit (valid but excluded finding)
250:          require(_commissionPercentage != daoCommissionPercentage, "Same commission percentage");

/// @audit (valid but excluded finding)
256:          require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

/// @audit (valid but excluded finding)
257:          require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

/// @audit (valid but excluded finding)
258:          require(numberOfKnots == 0, "Cannot change ticker once house is created");

/// @audit (valid but excluded finding)
268:          require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");

/// @audit (valid but excluded finding)
279:          require(_nodeRunner != address(0), "Zero address");

/// @audit (valid but excluded finding)
280:          require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

/// @audit (valid but excluded finding)
290:          require(_newRepresentative != address(0), "Zero address");

/// @audit (valid but excluded finding)
291:          require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

/// @audit (valid but excluded finding)
294:          require(smartWallet != address(0), "No smart wallet");

/// @audit (valid but excluded finding)
295:          require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");

/// @audit (valid but excluded finding)
296:          require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

/// @audit (valid but excluded finding)
309:          require(_newRepresentative != address(0), "Zero address");

/// @audit (valid but excluded finding)
312:          require(smartWallet != address(0), "No smart wallet");

/// @audit (valid but excluded finding)
313:          require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");

/// @audit (valid but excluded finding)
314:          require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

/// @audit (valid but excluded finding)
327:          require(_recipient != address(0), "Zero address");

/// @audit (valid but excluded finding)
328:          require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

/// @audit (valid but excluded finding)
331:          require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");

/// @audit (valid but excluded finding)
332:          require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

/// @audit (valid but excluded finding)
333:          require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");

/// @audit (valid but excluded finding)
359:          require(wallet != address(0), "Wallet does not exist");

/// @audit (valid but excluded finding)
362:          require(newRunnerCurrentWallet == address(0), "New runner has a wallet");

/// @audit (valid but excluded finding)
384:          require(_blsPubKeys.length > 0, "No BLS keys specified");

/// @audit (valid but excluded finding)
385:          require(_recipient != address(0), "Zero address");

/// @audit (valid but excluded finding)
388:          require(smartWallet != address(0), "Unknown node runner");

/// @audit (valid but excluded finding)
391:              require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

/// @audit (valid but excluded finding)
394:              require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

/// @audit (valid but excluded finding)
410:          require(transferResult, "Failed to transfer");

/// @audit (valid but excluded finding)
414:              require(transferResult, "Failed to transfer");

/// @audit (valid but excluded finding)
430:          require(len >= 1, "No value provided");

/// @audit (valid but excluded finding)
431:          require(len == _blsSignatures.length, "Unequal number of array values");

/// @audit (valid but excluded finding)
432:          require(msg.value == len * 4 ether, "Insufficient ether provided");

/// @audit (valid but excluded finding)
433:          require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");

/// @audit (valid but excluded finding)
434:          require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

/// @audit (valid but excluded finding)
435:          require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

/// @audit (valid but excluded finding)
453:              require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");

/// @audit (valid but excluded finding)
459:              require(result, "Transfer failed");

/// @audit (valid but excluded finding)
467:              require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

/// @audit (valid but excluded finding)
523:          require(numOfValidators > 0, "No data");

/// @audit (valid but excluded finding)
524:          require(numOfValidators == _ciphertexts.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
525:          require(numOfValidators == _aesEncryptorKeys.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
526:          require(numOfValidators == _encryptionSignatures.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
527:          require(numOfValidators == _dataRoots.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
532:              require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

/// @audit (valid but excluded finding)
535:              require(associatedSmartWallet != address(0), "Unknown BLS public key");

/// @audit (valid but excluded finding)
574:          require(numOfKnotsToProcess > 0, "Empty array");

/// @audit (valid but excluded finding)
575:          require(numOfKnotsToProcess == _beaconChainBalanceReports.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
576:          require(numOfKnotsToProcess == _reportSignatures.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
580:              require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

/// @audit (valid but excluded finding)
649:          require(_dao != address(0), "Zero address");

/// @audit (valid but excluded finding)
650:          require(_syndicateFactory != address(0), "Zero address");

/// @audit (valid but excluded finding)
651:          require(_smartWalletFactory != address(0), "Zero address");

/// @audit (valid but excluded finding)
652:          require(_brand != address(0), "Zero address");

/// @audit (valid but excluded finding)
653:          require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");

/// @audit (valid but excluded finding)
654:          require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

/// @audit (valid but excluded finding)
676:          require(_nodeRunner != address(0), "Zero address");

/// @audit (valid but excluded finding)
679:              require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

/// @audit (valid but excluded finding)
913:          require(_received > 0, "Nothing received");

/// @audit (valid but excluded finding)
927:          require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

/// @audit (valid but excluded finding)
930:          require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");

/// @audit (valid but excluded finding)
931:          require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

/// @audit (valid but excluded finding)
934:          require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");

/// @audit (valid but excluded finding)
935:          require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");

/// @audit (valid but excluded finding)
940:          require(_commissionPercentage <= MODULO, "Invalid commission");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L161

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

/// @audit (valid but excluded finding)
19:           require(_lpTokenImplementation != address(0), "Address cannot be zero");

/// @audit (valid but excluded finding)
33:           require(address(_deployer) != address(0), "Zero address");

/// @audit (valid but excluded finding)
34:           require(bytes(_tokenSymbol).length != 0, "Symbol cannot be zero");

/// @audit (valid but excluded finding)
35:           require(bytes(_tokenName).length != 0, "Name cannot be zero");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPTokenFactory.sol#L19

```solidity
File: contracts/liquid-staking/LPToken.sol

/// @audit (valid but excluded finding)
23:           require(msg.sender == deployer, "Only savETH vault");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPToken.sol#L23

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

/// @audit (valid but excluded finding)
51:           require(_liquidStakingManagerImplementation != address(0), "Zero Address");

/// @audit (valid but excluded finding)
52:           require(_syndicateFactory != address(0), "Zero Address");

/// @audit (valid but excluded finding)
53:           require(_lpTokenFactory != address(0), "Zero Address");

/// @audit (valid but excluded finding)
54:           require(_smartWalletFactory != address(0), "Zero Address");

/// @audit (valid but excluded finding)
55:           require(_brand != address(0), "Zero Address");

/// @audit (valid but excluded finding)
56:           require(_savETHVaultDeployer != address(0), "Zero Address");

/// @audit (valid but excluded finding)
57:           require(_stakingFundsVaultDeployer != address(0), "Zero Address");

/// @audit (valid but excluded finding)
58:           require(_optionalGatekeeperDeployer != address(0), "Zero Address");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LSDNFactory.sol#L51

```solidity
File: contracts/liquid-staking/SavETHVault.sol

/// @audit (valid but excluded finding)
50:           require(msg.sender == address(liquidStakingManager), "Not the savETH vault manager");

/// @audit (valid but excluded finding)
59:           require(numOfValidators > 0, "Empty arrays");

/// @audit (valid but excluded finding)
60:           require(numOfValidators == _amounts.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
64:               require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

/// @audit (valid but excluded finding)
76:           require(msg.value == totalAmount, "Invalid ETH amount attached");

/// @audit (valid but excluded finding)
84:           require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

/// @audit (valid but excluded finding)
90:           require(msg.value == _amount, "Must provide correct amount of ETH");

/// @audit (valid but excluded finding)
101:          require(numOfTokens > 0, "Empty arrays");

/// @audit (valid but excluded finding)
102:          require(numOfTokens == _amounts.length, "Inconsistent array length");

/// @audit (valid but excluded finding)
114:          require(numOfTokens > 0, "Empty arrays");

/// @audit (valid but excluded finding)
115:          require(numOfTokens == _amounts.length, "Inconsisent array length");

/// @audit (valid but excluded finding)
127:          require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

/// @audit (valid but excluded finding)
128:          require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

/// @audit (valid but excluded finding)
161:                  require(dETHBalance >= 24 ether, "Nothing to withdraw");

/// @audit (valid but excluded finding)
186:          require(isStaleLiquidity, "Liquidity is still fresh");

/// @audit (valid but excluded finding)
190:          require(result, "Transfer failed");

/// @audit (valid but excluded finding)
204:          require(_amount >= 24 ether, "Amount cannot be less than 24 ether");

/// @audit (valid but excluded finding)
205:          require(address(this).balance >= _amount, "Insufficient withdrawal amount");

/// @audit (valid but excluded finding)
206:          require(_smartWallet != address(0), "Zero address");

/// @audit (valid but excluded finding)
207:          require(_smartWallet != address(this), "This address");

/// @audit (valid but excluded finding)
210:          require(result, "Transfer failed");

/// @audit (valid but excluded finding)
236:          require(_liquidStakingManagerAddress != address(0), "Zero address");

/// @audit (valid but excluded finding)
237:          require(address(_lpTokenFactory) != address(0), "Zero address");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L50

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

/// @audit (valid but excluded finding)
51:           require(msg.sender == address(liquidStakingNetworkManager), "Only network manager");

/// @audit (valid but excluded finding)
71:           require(numOfValidators > 0, "Empty arrays");

/// @audit (valid but excluded finding)
72:           require(numOfValidators == _amounts.length, "Inconsistent array lengths");

/// @audit (valid but excluded finding)
79:               require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

/// @audit (valid but excluded finding)
107:          require(msg.value == totalAmount, "Invalid ETH amount attached");

/// @audit (valid but excluded finding)
114:          require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

/// @audit (valid but excluded finding)
120:          require(msg.value == _amount, "Must provide correct amount of ETH");

/// @audit (valid but excluded finding)
150:          require(numOfTokens > 0, "Empty arrays");

/// @audit (valid but excluded finding)
151:          require(numOfTokens == _amounts.length, "Inconsistent array length");

/// @audit (valid but excluded finding)
154:              require(address(token) != address(0), "No ETH staked for specified BLS key");

/// @audit (valid but excluded finding)
164:          require(numOfTokens > 0, "Empty arrays");

/// @audit (valid but excluded finding)
165:          require(numOfTokens == _amounts.length, "Inconsistent array length");

/// @audit (valid but excluded finding)
175:          require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

/// @audit (valid but excluded finding)
176:          require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");

/// @audit (valid but excluded finding)
177:          require(address(_lpToken) != address(0), "Zero address specified");

/// @audit (valid but excluded finding)
184:          require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");

/// @audit (valid but excluded finding)
191:          require(result, "Transfer failed");

/// @audit (valid but excluded finding)
229:              require(address(token) != address(0), "Invalid BLS key");

/// @audit (valid but excluded finding)
230:              require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");

/// @audit (valid but excluded finding)
240:          require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

/// @audit (valid but excluded finding)
241:          require(_amount <= address(this).balance, "Not enough ETH to withdraw");

/// @audit (valid but excluded finding)
242:          require(_wallet != address(0), "Zero address");

/// @audit (valid but excluded finding)
245:          require(result, "Transfer failed");

/// @audit (valid but excluded finding)
267:              require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");

/// @audit (valid but excluded finding)
320:              require(blsPubKey.length > 0, "Invalid token");

/// @audit (valid but excluded finding)
346:              require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");

/// @audit (valid but excluded finding)
361:          require(_syndicate != address(0), "Invalid configuration");

/// @audit (valid but excluded finding)
372:          require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");

/// @audit (valid but excluded finding)
373:          require(address(_lpTokenFactory) != address(0), "Zero Address");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L51

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

/// @audit (valid but excluded finding)
57:           require(_recipient != address(0), "Zero address");

/// @audit (valid but excluded finding)
68:                   require(success, "Failed to transfer");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L57

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

/// @audit (valid but excluded finding)
79:           require(result, "Failed to execute");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWallet.sol#L79

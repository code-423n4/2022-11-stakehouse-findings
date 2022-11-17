# Gas

### Suggestions
- [G-1] Upgrade openzeppelin contract to latest versions
- [G-2] State variables that never change should be declared `immutable` or `constant`
- [G-3] Cache functions inside loops
- [G-4] Remove unnecesary variable `balance` on [SyndicateRewardsProcessor.sol#L58](https://github.com/code-423n4/2022-11-stakehouse/blob/main/
- [G-5] Use `X = X + A` is cheaper in gas cost than `X += A`
- [G-6] Use `unchecked` blocks to save gas
- [G-7] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow
- [G-8] Functions guaranteed to revert when called by normal users can be marked `payable`


## Upgrade openzeppelin contract to latest versions

You are currently using version `4.5.0`, recent stable version release of openzeppelin has multiple fixes and gas optimizations.

```diff
diff --git a/package.json b/package.json
index 8c3f10b..83a67d5 100644
--- a/package.json
+++ b/package.json
@@ -12,8 +12,8 @@
     "@blockswaplab/stakehouse-solidity-api": "2.1.0",
     "@nomiclabs/hardhat-ethers": "^2.2.0",
     "@nomiclabs/hardhat-waffle": "^2.0.3",
-    "@openzeppelin/contracts": "^4.5.0",
-    "@openzeppelin/contracts-upgradeable": "4.5.0",
+    "@openzeppelin/contracts": "^4.8.0",
+    "@openzeppelin/contracts-upgradeable": "4.8.0",
     "dotenv": "^16.0.3",
     "ethers": "^5.7.1"
   }
```

Snapshot diff result ver `4.5.0` vs `4.8.0`

```
testWithdrawETHForStakingWorksAsExpected() (gas: 774 (0.009%)) 
testRegisterKeysAndWithdrawETHAsNodeRunner() (gas: 70 (0.011%)) 
testBothCollateralizedAndSlotClaim() (gas: -64 (-0.012%)) 
testClaimAsCollateralizedSlotOwner() (gas: 36 (0.014%)) 
testExpansionOfKnotSet() (gas: -192 (-0.018%)) 
testThreeKnotsMultipleStakers() (gas: -500 (-0.041%)) 
testOneKnotWithMultipleFreeFloatingStakers() (gas: -392 (-0.045%)) 
testStakeFreeFloatingReceiveETHAndThenClaim() (gas: -264 (-0.053%)) 
testCanReceiveETHAndWithdraw() (gas: 30 (0.057%)) 
testMint() (gas: -110 (-0.098%)) 
testWithdrawETHForStakingRevertsWhenAmountIsZero() (gas: 24 (0.116%)) 
testBurnLPRevertsWhenAmountIsZero() (gas: 24 (0.175%)) 
testBurn() (gas: -164 (-0.183%)) 
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -49309 (-1.458%)) 
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -48858 (-1.480%)) 
testReceiveETHAndDistributeDuringLPTransfers() (gas: -49260 (-1.525%)) 
testReceiveETHAndDistributeToMultipleLPs() (gas: -49101 (-1.544%)) 
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -49909 (-1.601%)) 
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -49054 (-1.647%)) 
testStakeKnotAndMintDerivatives() (gas: -49155 (-1.787%)) 
testThatAfterFirstKnotSecondKnotOnwardsJoinsTheLSDHouse() (gas: -114066 (-2.108%)) 
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormationAndBalIncrease() (gas: -21154 (-3.233%)) 
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormation() (gas: -21154 (-3.348%)) 
testSecondDepositForKnotMintsSameLP() (gas: -20937 (-3.381%)) 
testFirstDepositsForKnotAreBetweenOneAndTwentyFour(uint256) (gas: -20823 (-3.791%)) 
testFirstDepositDeploysAnLPToken() (gas: -20823 (-3.797%)) 
testBurnLPForETHWorksAsExpected() (gas: -20874 (-3.824%)) 
testBurnLPRevertsWhenETHIsStaked() (gas: -20799 (-3.832%)) 
testBurnLPRevertsWhenBalanceIsZero() (gas: -20803 (-3.863%)) 
testBurnLPBeforeETHIsStaked() (gas: -20877 (-3.864%)) 
testDepositETHForStaking() (gas: -42460 (-3.882%)) 
testBatchDepositETHForStakingCanSuccessfullyDepositForMultipleValidators() (gas: -63885 (-4.489%)) 
testBurnLPTokensWorksAsExpectedForMultipleValidators() (gas: -64062 (-4.556%)) 
testBatchDepositETHForStakingRevertsWhenETHNotAttached() (gas: -63885 (-4.567%)) 
Overall gas change: -881976 (-63.645%)
```

## State variables that never change should be declared `immutable` or `constant`

Avoids a Gsset (**20000 gas**) in the constructor, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmacces - **100 gas**) with a `PUSH32` (**3 gas**).

While it's not possible to use immutable on Proxy contracts, some variables can be declared as a hard-coded constant and get the same gas savings

Variables that can be declared as `constant`;
- `MODULO` on [LiquidStakingManager.sol#L158](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158)

Variables that can be declared as `immutable`;
- `pool` on [GiantLP.sol#L11](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L11)
- `transferHookProcessor` on [GiantLP.sol#L14](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L14)
- `lpTokenImplementation` on [LPTokenFactory.sol#L15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15)
- `syndicateImplementation` on [SyndicateFactory.sol#L13](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L13)
- `liquidStakingManager` on [OptionalHouseGatekeeper.sol#L12](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L12)
- `implementation` on [SavETHVaultDeployer.sol#L13](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L13)
- `implementation` on [StakingFundsVaultDeployer.sol#L13](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L13)
- `liquidStakingManagerImplementation` on [](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L15)
- `lpTokenFactory` on [LSDNFactory.sol#L21](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L21)
- `smartWalletFactory` on [LSDNFactory.sol#L24](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L24)
- `brand` on [LSDNFactory.sol#L27](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L27)
- `savETHVaultDeployer` on [LSDNFactory.sol#L30](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L30)
- `stakingFundsVaultDeployer` on [LSDNFactory.sol#L33](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L33)
- `optionalGatekeeperDeployer` on [LSDNFactory.sol#L36](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L36)


**Diff gas snapshot according to test**
```
testThatAfterFirstKnotSecondKnotOnwardsJoinsTheLSDHouse() (gas: -4729 (-0.088%)) 
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -4366 (-0.129%)) 
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -4366 (-0.133%)) 
testReceiveETHAndDistributeDuringLPTransfers() (gas: -4366 (-0.135%)) 
testReceiveETHAndDistributeToMultipleLPs() (gas: -4366 (-0.138%)) 
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -4366 (-0.147%)) 
testStakeKnotAndMintDerivatives() (gas: -4490 (-0.164%)) 
testBatchDepositETHForStakingCanSuccessfullyDepositForMultipleValidators() (gas: -2363 (-0.166%)) 
testBurnLPTokensWorksAsExpectedForMultipleValidators() (gas: -2363 (-0.168%)) 
testBatchDepositETHForStakingRevertsWhenETHNotAttached() (gas: -2363 (-0.169%)) 
testDepositETHForStaking() (gas: -2242 (-0.205%)) 
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormationAndBalIncrease() (gas: -2121 (-0.324%)) 
testWithdrawETHForStakingWorksAsExpected() (gas: -27100 (-0.332%)) 
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormation() (gas: -2121 (-0.336%)) 
testSecondDepositForKnotMintsSameLP() (gas: -2121 (-0.342%)) 
testFirstDepositsForKnotAreBetweenOneAndTwentyFour(uint256) (gas: -2121 (-0.386%)) 
testFirstDepositDeploysAnLPToken() (gas: -2121 (-0.386%)) 
testBurnLPForETHWorksAsExpected() (gas: -2121 (-0.388%)) 
testBurnLPRevertsWhenETHIsStaked() (gas: -2121 (-0.390%)) 
testBurnLPBeforeETHIsStaked() (gas: -2121 (-0.392%)) 
testBurnLPRevertsWhenBalanceIsZero() (gas: -2121 (-0.394%)) 
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -13478 (-0.433%)) 
testSetupIsCorrect() (gas: -2121 (-21.765%)) 
Overall gas change: -102168 (-27.511%)
```

## Cache functions inside loops

### Cache `getAccountManager()` on [SavETHVault.sol#L63-L73](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L63-L73)
```diff
diff --git a/contracts/liquid-staking/SavETHVault.sol b/contracts/liquid-staking/SavETHVault.sol
index 576afb9..7327498 100644
--- a/contracts/liquid-staking/SavETHVault.sol
+++ b/contracts/liquid-staking/SavETHVault.sol
@@ -5,7 +5,7 @@ pragma solidity ^0.8.13;
 import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
 import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
 import { IDataStructures } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/IDataStructures.sol";
-
+import { IAccountManager } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/IAccountManager.sol";
 import { ILiquidStakingManager } from "../interfaces/ILiquidStakingManager.sol";
 
 import { StakingFundsVault } from "./StakingFundsVault.sol";
@@ -58,12 +58,12 @@ contract SavETHVault is Initializable, ETHPoolLPFactory, ReentrancyGuard {
         uint256 numOfValidators = _blsPublicKeyOfKnots.length;
         require(numOfValidators > 0, "Empty arrays");
         require(numOfValidators == _amounts.length, "Inconsistent array lengths");
-
+        IAccountManager _getAccountManager = getAccountManager();
         uint256 totalAmount;
         for (uint256 i; i < numOfValidators; ++i) {
             require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
             require(
-                getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
+                _getAccountManager.blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
                 "Lifecycle status must be one"
             );
```

### Cache `getSavETHRegistry()` on `burnLPToken` [SavETHVault.sol#L126](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L126)

```diff
diff --git a/contracts/liquid-staking/SavETHVault.sol b/contracts/liquid-staking/SavETHVault.sol
index 576afb9..e986bad 100644
--- a/contracts/liquid-staking/SavETHVault.sol
+++ b/contracts/liquid-staking/SavETHVault.sol
@@ -5,7 +5,7 @@ pragma solidity ^0.8.13;
 import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
 import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
 import { IDataStructures } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/IDataStructures.sol";
-
+import { ISavETHManager } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/ISavETHManager.sol";
 import { ILiquidStakingManager } from "../interfaces/ILiquidStakingManager.sol";
 
 import { StakingFundsVault } from "./StakingFundsVault.sol";
@@ -150,19 +150,19 @@ contract SavETHVault is Initializable, ETHPoolLPFactory, ReentrancyGuard {
             uint256 redemptionValue;
 
             KnotDETHDetails storage dETHDetails = dETHForKnot[blsPublicKeyOfKnot];
-
+            ISavETHManager _ISavETHManager = getSavETHRegistry();
             if(!dETHDetails.withdrawn) {
                 // withdraw dETH if not done already
 
                 // get dETH balance for the KNOT
-                uint256 dETHBalance = getSavETHRegistry().knotDETHBalanceInIndex(indexOwnedByTheVault, blsPublicKeyOfKnot);
-                uint256 savETHBalance = getSavETHRegistry().dETHToSavETH(dETHBalance);
+                uint256 dETHBalance = _ISavETHManager.knotDETHBalanceInIndex(indexOwnedByTheVault, blsPublicKeyOfKnot);
+                uint256 savETHBalance = _ISavETHManager.dETHToSavETH(dETHBalance);
                 // This require should never fail but is there for sanity purposes
                 require(dETHBalance >= 24 ether, "Nothing to withdraw");
 
                 // withdraw savETH from savETH index to the savETH vault
                 // contract gets savETH and not the dETH
-                getSavETHRegistry().addKnotToOpenIndex(liquidStakingManager.stakehouse(), blsPublicKeyOfKnot, address(this));
+                _ISavETHManager.addKnotToOpenIndex(liquidStakingManager.stakehouse(), blsPublicKeyOfKnot, address(this));
 
                 // update mapping
                 dETHDetails.withdrawn = true;
@@ -174,9 +174,9 @@ contract SavETHVault is Initializable, ETHPoolLPFactory, ReentrancyGuard {
             redemptionValue = (dETHDetails.savETHBalance * _amount) / 24 ether;
 
             // withdraw dETH (after burning the savETH)
-            getSavETHRegistry().withdraw(msg.sender, uint128(redemptionValue));
+            _ISavETHManager.withdraw(msg.sender, uint128(redemptionValue));
 
-            uint256 dETHRedeemed = getSavETHRegistry().savETHToDETH(redemptionValue);
+            uint256 dETHRedeemed = _ISavETHManager.savETHToDETH(redemptionValue);
 
             emit DETHRedeemed(msg.sender, dETHRedeemed);
             return redemptionValue;
```

### Cache `getDETH()` on `withdrawDETH` [GiantSavETHVaultPool.sol#L66](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L66)

```diff
diff --git a/contracts/liquid-staking/GiantSavETHVaultPool.sol b/contracts/liquid-staking/GiantSavETHVaultPool.sol
index 4edbd43..544ef2f 100644
--- a/contracts/liquid-staking/GiantSavETHVaultPool.sol
+++ b/contracts/liquid-staking/GiantSavETHVaultPool.sol
@@ -8,7 +8,7 @@ import { SavETHVault } from "./SavETHVault.sol";
 import { LPToken } from "./LPToken.sol";
 import { GiantPoolBase } from "./GiantPoolBase.sol";
 import { LSDNFactory } from "./LSDNFactory.sol";
-
+import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
 /// @notice A giant pool that can provide protected deposit liquidity to any liquid staking network
 contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {
 
@@ -72,9 +72,9 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {
         require(numOfVaults > 0, "Empty arrays");
         require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
         require(numOfVaults == _amounts.length, "Inconsistent arrays");
-
+        IERC20 _DETH = getDETH();
         // Firstly capture current dETH balance and see how much has been deposited after the loop
-        uint256 dETHReceivedFromAllSavETHVaults = getDETH().balanceOf(address(this));
+        uint256 dETHReceivedFromAllSavETHVaults = _DETH.balanceOf(address(this));
         for (uint256 i; i < numOfVaults; ++i) {
             SavETHVault vault = SavETHVault(_savETHVaults[i]);
 
@@ -102,10 +102,10 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {
         }
 
         // Calculate how much dETH has been received from burning
-        dETHReceivedFromAllSavETHVaults = getDETH().balanceOf(address(this)) - dETHReceivedFromAllSavETHVaults;
+        dETHReceivedFromAllSavETHVaults = _DETH.balanceOf(address(this)) - dETHReceivedFromAllSavETHVaults;
 
         // Send giant LP holder dETH owed
-        getDETH().transfer(msg.sender, dETHReceivedFromAllSavETHVaults);
+        _DETH.transfer(msg.sender, dETHReceivedFromAllSavETHVaults);
     }
 
     /// @notice Any ETH supplied to a BLS key registered with a liquid staking network can be rotated to another key if it never gets staked
```

## Remove unnecesary variable `balance` on [SyndicateRewardsProcessor.sol#L58](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L58)

```diff
diff --git a/contracts/liquid-staking/SyndicateRewardsProcessor.sol b/contracts/liquid-staking/SyndicateRewardsProcessor.sol
index 81be706..79ea19c 100644
--- a/contracts/liquid-staking/SyndicateRewardsProcessor.sol
+++ b/contracts/liquid-staking/SyndicateRewardsProcessor.sol
@@ -55,10 +55,9 @@ abstract contract SyndicateRewardsProcessor {
         address _recipient
     ) internal {
         require(_recipient != address(0), "Zero address");
-        uint256 balance = _balance;
-        if (balance > 0) {
+        if (_balance > 0) {
             // Calculate how much ETH rewards the address is owed / due 
-            uint256 due = ((accumulatedETHPerLPShare * balance) / PRECISION) - claimed[_user][_token];
+            uint256 due = ((accumulatedETHPerLPShare * _balance) / PRECISION) - claimed[_user][_token];
             if (due > 0) {
                 claimed[_user][_token] = due;
```

## Use `X = X + A` is cheaper in gas cost than `X += A`

In the following example (optimizer = 10000) it's possible to demostrate that I = I + X cost less gas than I += X in state variable.

```
contract Test_Optimization {
    uint256 a = 1;
    function Add () external returns (uint256) {
        a = a + 1;
        return a;
    }
}

contract Test_Without_Optimization {
    uint256 a = 1;
    function Add () external returns (uint256) {
        a += 1;
        return a;
    }
}
```
Test_Optimization cost is 26324 gas
Test_Without_Optimization cost is 26440 gas
With this optimization it's possible to save 116 gas


Lines in the code:

- [StakingFundsVault.sol#L97](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L97)
- [StakingFundsVault.sol#L278](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L278)
- [Syndicate.sol#L430-L431](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L430-L431)



## Use `unchecked` blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

On [Syndicate.sol#L658-L666](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L658-L666)
```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..dd0c186 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -655,15 +655,16 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
             // worst that will happen is that they will just waste gas. this is needed for unstaking
             if (unclaimedUserShare > 0) {
                 // Increase total claimed at the contract level
-                totalClaimed += unclaimedUserShare;
-
-                // Work out which accumulated ETH per free floating share value was used
-                uint256 accumulatedETHPerShare = _getCorrectAccumulatedETHPerFreeFloatingShareForBLSPublicKey(_blsPubKey);
+                unchecked {
+                    totalClaimed += unclaimedUserShare;
 
-                // Update the total ETH claimed by the free floating SLOT holder based on their share of sETH
-                sETHUserClaimForKnot[_blsPubKey][msg.sender] =
-                (accumulatedETHPerShare * sETHStakedBalanceForKnot[_blsPubKey][msg.sender]) / PRECISION;
+                    // Work out which accumulated ETH per free floating share value was used
+                    uint256 accumulatedETHPerShare = _getCorrectAccumulatedETHPerFreeFloatingShareForBLSPublicKey(_blsPubKey);
 
+                    // Update the total ETH claimed by the free floating SLOT holder based on their share of sETH
+                    sETHUserClaimForKnot[_blsPubKey][msg.sender] =
+                    (accumulatedETHPerShare * sETHStakedBalanceForKnot[_blsPubKey][msg.sender]) / PRECISION;
+                }
                 // Send ETH to user
                 (bool success,) = _recipient.call{value: unclaimedUserShare}("");
                 if (!success) revert TransferFailed();
```


On [ETHPoolLPFactory.sol#L144](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L144)
```diff
diff --git a/contracts/liquid-staking/ETHPoolLPFactory.sol b/contracts/liquid-staking/ETHPoolLPFactory.sol
index caa0cf8..cadd4a8 100644
--- a/contracts/liquid-staking/ETHPoolLPFactory.sol
+++ b/contracts/liquid-staking/ETHPoolLPFactory.sol
@@ -141,7 +141,7 @@ abstract contract ETHPoolLPFactory is StakehouseAPI {
                              LPToken(lpTokenFactory.deployLPToken(address(this), address(0), tokenSymbol, tokenName));
 
             // increase the count of LP tokens
-            numberOfLPTokensIssued++;
+            unchecked { ++numberOfLPTokensIssued; }
 
             // register the BLS Public Key with the LP token
             lpTokenForKnot[_blsPublicKeyOfKnot] = newLPToken;
```


On [SyndicateRewardsProcessor.sol#L65](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L65)

```diff
diff --git a/contracts/liquid-staking/SyndicateRewardsProcessor.sol b/contracts/liquid-staking/SyndicateRewardsProcessor.sol
index 81be706..fdbced4 100644
--- a/contracts/liquid-staking/SyndicateRewardsProcessor.sol
+++ b/contracts/liquid-staking/SyndicateRewardsProcessor.sol
@@ -62,7 +62,7 @@ abstract contract SyndicateRewardsProcessor {
             if (due > 0) {
                 claimed[_user][_token] = due;
 
-                totalClaimed += due;
+                unchecked { totalClaimed += due; }
 
                 (bool success, ) = _recipient.call{value: due}("");
                 require(success, "Failed to transfer");
```

On [SyndicateRewardsProcessor.sol#L65](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L65)
```
                unchecked { totalClaimed += due; }
```

On [LiquidStakingManager.sol#L782](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L782) instead of `numberOfKnots += 1;`
```
        unchecked { ++numberOfKnots; }
```

On [GiantPoolBase.sol#L39](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L39);
```
        unchecked { idleETH += msg.value; }
```

On [LiquidStakingManager.sol#L770](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L770)
```
        unchecked { ++stakedKnotsOfSmartWallet[smartWallet]; }
```

On [LiquidStakingManager.sol#L839](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L839)
```
        unchecked { ++numberOfKnots; }
```

On [StakingFundsVault.sol#L58](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L58)
```
        unchecked { totalShares += 4 ether; }
```

On [StakingFundsVault.sol#L97](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L97)
```
            unchecked { totalAmount += amount; }
```

On [StakingFundsVault.sol#L278](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L278)
```
            unchecked { totalAccumulated += previewAccumulatedETH(_user, token); }
```

On [StakingFundsVault.sol#L287](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L287)
```
            unchecked { totalAccumulated += previewAccumulatedETH(_user, _token[i]); }
```

On [SyndicateRewardsProcessor.sol#L65](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L65)
```
                unchecked { totalClaimed += due; }
```

On [Syndicate.sol#L190](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L190)
```
            unchecked { accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed; }
```

On [Syndicate.sol#L225-L228](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L225-L228)
```
          unchecked {
            totalFreeFloatingShares += _sETHAmount;
            sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
            sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
            sETHUserClaimForKnot[_blsPubKey][_onBehalfOf] = (_sETHAmount * accumulatedETHPerFreeFloatingShare) / PRECISION;
          }
```

## `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

Instead of;
```solidity
for (uint256 i; i < len; i++) {
  // code
}
```
Use this pattern;
```solidity
for (uint256 i; i < len;) {
  // code
  unchecked { i++; }
}
```

- [contracts/liquid-staking/GiantMevAndFeesPool.sol:38](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.so#L:38)
- [contracts/liquid-staking/GiantMevAndFeesPool.sol:64](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.so#L:64)
- [contracts/liquid-staking/GiantMevAndFeesPool.sol:90](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.so#L:90)
- [contracts/liquid-staking/GiantMevAndFeesPool.sol:117](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L117)
- [contracts/liquid-staking/GiantMevAndFeesPool.sol:135](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L135)
- [contracts/liquid-staking/GiantMevAndFeesPool.sol:183](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L183)
- [contracts/liquid-staking/StakingFundsVault.sol:78](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.so#L:78)
- [contracts/liquid-staking/StakingFundsVault.sol:152](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L152)
- [contracts/liquid-staking/StakingFundsVault.sol:166](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L166)
- [contracts/liquid-staking/StakingFundsVault.sol:203](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L203)
- [contracts/liquid-staking/StakingFundsVault.sol:266](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L266)
- [contracts/liquid-staking/StakingFundsVault.sol:276](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L276)
- [contracts/liquid-staking/StakingFundsVault.sol:286](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L286)
- [contracts/liquid-staking/SavETHVault.sol:63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.so#L:63)
- [contracts/liquid-staking/SavETHVault.sol:103](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L103)
- [contracts/liquid-staking/SavETHVault.sol:116](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L116)
- [contracts/liquid-staking/LiquidStakingManager.sol:392](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L392)
- [contracts/liquid-staking/LiquidStakingManager.sol:465](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L465)
- [contracts/liquid-staking/LiquidStakingManager.sol:538](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L538)
- [contracts/liquid-staking/LiquidStakingManager.sol:587](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L587)
- [contracts/liquid-staking/ETHPoolLPFactory.sol:63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.so#L:63)
- [contracts/liquid-staking/GiantSavETHVaultPool.sol:42](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.so#L:42)
- [contracts/liquid-staking/GiantSavETHVaultPool.sol:78](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.so#L:78)
- [contracts/liquid-staking/GiantSavETHVaultPool.sol:128](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L128)
- [contracts/liquid-staking/GiantSavETHVaultPool.sol:146](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L146)
- [contracts/liquid-staking/GiantPoolBase.sol:76](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.so#L:76)
- [contracts/syndicate/Syndicate.sol:211](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L211)
- [contracts/syndicate/Syndicate.sol:259](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L259)
- [contracts/syndicate/Syndicate.sol:301](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L301)
- [contracts/syndicate/Syndicate.sol:346](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L346)
- [contracts/syndicate/Syndicate.sol:420](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L420)
- [contracts/syndicate/Syndicate.sol:513](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L513)
- [contracts/syndicate/Syndicate.sol:560](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L560)
- [contracts/syndicate/Syndicate.sol:585](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L585)
- [contracts/syndicate/Syndicate.sol:598](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L598)
- [contracts/syndicate/Syndicate.sol:648](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L648)


## Functions guaranteed to revert when called by normal users can be marked `payable`

On:

- [contracts/smart-wallet/OwnableSmartWallet.sol:41](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L41)
- [contracts/smart-wallet/OwnableSmartWallet.sol:52](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L52)
- [contracts/smart-wallet/OwnableSmartWallet.sol:67](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L67)
- [contracts/smart-wallet/OwnableSmartWallet.sol:114](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L114)
- [contracts/syndicate/Syndicate.sol:145](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L145)
- [contracts/syndicate/Syndicate.sol:154](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L154)
- [contracts/syndicate/Syndicate.sol:161](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L161)
- [contracts/syndicate/Syndicate.sol:168](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L168)

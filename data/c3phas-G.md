## FINDINGS
NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
Throught the report some places might be denoted with audit tags to show the actual place affected.

### Using immutable on variables that are only set in the constructor and never after 
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas)

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L12
```solidity
File: /contracts/liquid-staking/OptionalHouseGatekeeper.sol
12:    ILiquidStakingManager public liquidStakingManager;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVaultDeployer.sol#L13
```solidity
File: /contracts/liquid-staking/SavETHVaultDeployer.sol
13:    address implementation;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L13
```solidity
File: /contracts/liquid-staking/StakingFundsVaultDeployer.sol
13:    address implementation;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPTokenFactory.sol#L15
```solidity
File: /contracts/liquid-staking/LPTokenFactory.sol
15:    address public lpTokenImplementation;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L11
```solidity
File: /contracts/liquid-staking/GiantLP.sol
11:    address public pool;

14:    ITransferHookProcessor public transferHookProcessor;
```


https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/SyndicateFactory.sol#L13
```solidity
File: /contracts/syndicate/SyndicateFactory.sol
13:    address public syndicateImplementation;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LSDNFactory.sol#L15
```solidity
File:/contracts/liquid-staking/LSDNFactory.sol
15:    address public liquidStakingManagerImplementation;

18:    address public syndicateFactory;

21:    address public lpTokenFactory;

24:    address public smartWalletFactory;

27:    address public brand;

30:    address public savETHVaultDeployer;

33:    address public stakingFundsVaultDeployer;

36:    address public optionalGatekeeperDeployer;
```

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L52-L64
### GiantPoolBase.sol.withdrawETH(): idleETH should be cached
```solidity
File: /contracts/liquid-staking/GiantPoolBase.sol
52:    function withdrawETH(uint256 _amount) external nonReentrant {

55:        require(idleETH >= _amount, "Come back later or withdraw less ETH");//@audit: 1st SLOAD

	57:        idleETH -= _amount; //@audit: 2nd SLOAD
```

```diff
diff --git a/contracts/liquid-staking/GiantPoolBase.sol b/contracts/liquid-staking/GiantPoolBase.sol
index 8a8ff70..0ca443d 100644
--- a/contracts/liquid-staking/GiantPoolBase.sol
+++ b/contracts/liquid-staking/GiantPoolBase.sol
@@ -52,9 +52,10 @@ contract GiantPoolBase is ReentrancyGuard {
     function withdrawETH(uint256 _amount) external nonReentrant {
         require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
         require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");
-        require(idleETH >= _amount, "Come back later or withdraw less ETH");
+        uint256 _idleETH = idleETH;
+        require(_idleETH >= _amount, "Come back later or withdraw less ETH");

-        idleETH -= _amount;
+        idleETH = _idleETH - _amount;

         lpTokenETH.burn(msg.sender, _amount);
         (bool success,) = msg.sender.call{value: _amount}("");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L52-L64
### GiantPoolBase.sol.withdrawETH(): lpTokenETH should be cached
```solidity
File: /contracts/liquid-staking/GiantPoolBase.sol
52:    function withdrawETH(uint256 _amount) external nonReentrant {
53:        require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
54:        require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance"); //@audit: 1st SLOAD

59:        lpTokenETH.burn(msg.sender, _amount);//@audit: 2nd SLOAD
```

```diff
diff --git a/contracts/liquid-staking/GiantPoolBase.sol b/contracts/liquid-staking/GiantPoolBase.sol
index 8a8ff70..da57661 100644
--- a/contracts/liquid-staking/GiantPoolBase.sol
+++ b/contracts/liquid-staking/GiantPoolBase.sol
@@ -51,12 +51,13 @@ contract GiantPoolBase is ReentrancyGuard {
     /// @param _amount of LP tokens user is burning in exchange for same amount of ETH
     function withdrawETH(uint256 _amount) external nonReentrant {
         require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
-        require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");
+        GiantLP  _lpTokenETH = lpTokenETH;
+        require(_lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");
         require(idleETH >= _amount, "Come back later or withdraw less ETH");

         idleETH -= _amount;

-        lpTokenETH.burn(msg.sender, _amount);
+        _lpTokenETH.burn(msg.sender, _amount);
         (bool success,) = msg.sender.call{value: _amount}("");
         require(success, "Failed to transfer ETH");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L66-L109
### GiantSavETHVaultPool.sol.withdrawDETH(): lpTokenETH should be cached
```solidity
File:/contracts/liquid-staking/GiantSavETHVaultPool.sol
66:    function withdrawDETH(

91:                // Giant LP is burned 1:1 with LPs from sub-networks
92:                require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");//@audit: 1st SLOAD

94:                // Burn giant LP from user before sending them dETH
95:                lpTokenETH.burn(msg.sender, amount);//@audit: 2nd SLOAD
```

```diff
diff --git a/contracts/liquid-staking/GiantSavETHVaultPool.sol b/contracts/liquid-staking/GiantSavETHVaultPool.sol
index 4edbd43..9b50132 100644
--- a/contracts/liquid-staking/GiantSavETHVaultPool.sol
+++ b/contracts/liquid-staking/GiantSavETHVaultPool.sol
@@ -87,12 +87,12 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {
                 _assertUserHasEnoughGiantLPToClaimVaultLP(token, amount);

                 require(vault.isDETHReadyForWithdrawal(address(token)), "dETH is not ready for withdrawal");
-
+                GiantLP  _lpTokenETH = lpTokenETH;
                 // Giant LP is burned 1:1 with LPs from sub-networks
-                require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");
+                require(_lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");

                 // Burn giant LP from user before sending them dETH
-                lpTokenETH.burn(msg.sender, amount);
+                _lpTokenETH.burn(msg.sender, amount);

                 emit LPBurnedForDETH(address(token), msg.sender, amount);
             }
```


https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L56-L79
### GiantMevAndFeesPool.sol.claimRewards(): lpTokenETH should be cached
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
56:    function claimRewards(

73:        _distributeETHRewardsToUserForToken(
74:            msg.sender,
75:            address(lpTokenETH),
76:            lpTokenETH.balanceOf(msg.sender),
77:            _recipient
78:        );
79:    }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..c0ac294 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -69,11 +69,12 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
         }

         updateAccumulatedETHPerLP();
+        GiantLP  _lpTokenETH = lpTokenETH;

         _distributeETHRewardsToUserForToken(
             msg.sender,
-            address(lpTokenETH),
-            lpTokenETH.balanceOf(msg.sender),
+            address(_lpTokenETH),
+            _lpTokenETH.balanceOf(msg.sender),
             _recipient
         );
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L82-L98
### GiantMevAndFeesPool.sol.previewAccumulatedETH(): lpTokenETH should be cached
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
82:    function previewAccumulatedETH(

97:        return _previewAccumulatedETH(_user, address(lpTokenETH), lpTokenETH.balanceOf(_user), lpTokenETH.totalSupply(), accumulated);
98:    }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..47af7ae 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -93,8 +93,8 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
                 _lpTokens[i]
             );
         }
-
-        return _previewAccumulatedETH(_user, address(lpTokenETH), lpTokenETH.balanceOf(_user), lpTokenETH.totalSupply(), accumulated);
+        GiantLP  _lpTokenETH = lpTokenETH;
+        return _previewAccumulatedETH(_user, address(_lpTokenETH), _lpTokenETH.balanceOf(_user), _lpTokenETH.totalSupply(), accumulated);
     }

     /// @notice Any ETH supplied to a BLS key registered with a liquid staking network can be rotated to another key if it never gets staked
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L146-L167
### GiantMevAndFeesPool.sol.beforeTokenTransfer(): lpTokenETH should be cached
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
146:    function beforeTokenTransfer(address _from, address _to, uint256) external {
        require(msg.sender == address(lpTokenETH), "Caller is not giant LP");//@audit: 1st SLOAD

151:        if (_from != address(0)) {
152:            _distributeETHRewardsToUserForToken(
153:                _from,
154:                address(lpTokenETH),//@audit: 2nd SLOAD
155:                lpTokenETH.balanceOf(_from),//@audit: 3rd SLOAD
156:                _from
157:            );
158:        }

161:        _distributeETHRewardsToUserForToken(
162:            _to,
163:            address(lpTokenETH),//@audit: 4th SLOAD
164:            lpTokenETH.balanceOf(_to),//@audit: 5th SLOAD
165:            _to
166:        );
167:    }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..3ddb559 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -144,15 +144,16 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate

     /// @notice Allow giant LP token to notify pool about transfers so the claimed amounts can be processed
     function beforeTokenTransfer(address _from, address _to, uint256) external {
-        require(msg.sender == address(lpTokenETH), "Caller is not giant LP");
+        GiantLP  _lpTokenETH = lpTokenETH;
+        require(msg.sender == address(_lpTokenETH), "Caller is not giant LP");
         updateAccumulatedETHPerLP();

         // Make sure that `_from` gets total accrued before transfer as post transferred anything owed will be wiped
         if (_from != address(0)) {
             _distributeETHRewardsToUserForToken(
                 _from,
-                address(lpTokenETH),
-                lpTokenETH.balanceOf(_from),
+                address(_lpTokenETH),
+                _lpTokenETH.balanceOf(_from),
                 _from
             );
         }
@@ -160,8 +161,8 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
         // Make sure that `_to` gets total accrued before transfer as post transferred anything owed will be wiped
         _distributeETHRewardsToUserForToken(
             _to,
-            address(lpTokenETH),
-            lpTokenETH.balanceOf(_to),
+            address(_lpTokenETH),
+            _lpTokenETH.balanceOf(_to),
             _to
         );
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L181-L193
### GiantMevAndFeesPool.sol.\_onWithdraw():lpTokenETH should be cached
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
181:    function _onWithdraw(LPToken[] calldata _lpTokens) internal override {

187:        _distributeETHRewardsToUserForToken(
188:            msg.sender,
189:            address(lpTokenETH),//@audit: 1st SLOAD
190:            lpTokenETH.balanceOf(msg.sender),//@audit: 2nd SLOAD
191:            msg.sender
192:        );
193:    }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..641c4c7 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -183,11 +183,12 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
         for (uint256 i; i < _lpTokens.length; ++i) {
             _lpTokens[i].transfer(address(this), _lpTokens[i].balanceOf(address(this)));
         }
+        GiantLP  _lpTokenETH = lpTokenETH;

         _distributeETHRewardsToUserForToken(
             msg.sender,
-            address(lpTokenETH),
-            lpTokenETH.balanceOf(msg.sender),
+            address(_lpTokenETH),
+            _lpTokenETH.balanceOf(msg.sender),
             msg.sender
         );
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L201-L204
### GiantMevAndFeesPool.sol.\_setClaimedToMax():  should be cached
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
201:    function _setClaimedToMax(address _user) internal {
202:        // New ETH stakers are not entitled to ETH earned by
203:        claimed[_user][address(lpTokenETH)] = (accumulatedETHPerLPShare * lpTokenETH.balanceOf(_user)) / PRECISION;
204:    }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..24f1bfb 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -199,7 +199,8 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate

     /// @dev Internal re-usable method for setting claimed to max for msg.sender
     function _setClaimedToMax(address _user) internal {
+         GiantLP  _lpTokenETH = lpTokenETH;
         // New ETH stakers are not entitled to ETH earned by
-        claimed[_user][address(lpTokenETH)] = (accumulatedETHPerLPShare * lpTokenETH.balanceOf(_user)) / PRECISION;
+        claimed[_user][address(_lpTokenETH)] = (accumulatedETHPerLPShare * _lpTokenETH.balanceOf(_user)) / PRECISION;
     }
 }

```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L199-L233
### StakingFundsVault.sol.claimRewards(): liquidStakingNetworkManager should be cached
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
199:    function claimRewards(

203:        for (uint256 i; i < _blsPubKeys.length; ++i) {
204:            require(
205:                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
206:                "Unknown BLS public key"
207:            );

215:            if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {

218:                _claimFundsFromSyndicateForDistribution(
219:                    liquidStakingNetworkManager.syndicate(),
220:                    _blsPubKeys
221:                );

```

```diff
diff --git a/contracts/liquid-staking/StakingFundsVault.sol b/contracts/liquid-staking/StakingFundsVault.sol
index ecc185c..8cc11ab 100644
--- a/contracts/liquid-staking/StakingFundsVault.sol
+++ b/contracts/liquid-staking/StakingFundsVault.sol
@@ -200,9 +200,10 @@ contract StakingFundsVault is
         address _recipient,
         bytes[] calldata _blsPubKeys
     ) external nonReentrant {
+         LiquidStakingManager  _liquidStakingNetworkManager = liquidStakingNetworkManager;
         for (uint256 i; i < _blsPubKeys.length; ++i) {
             require(
-                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
+                _liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
                 "Unknown BLS public key"
             );

@@ -212,11 +213,11 @@ contract StakingFundsVault is
                 "Derivatives not minted"
             );

-            if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {
+            if (i == 0 && !Syndicate(payable(_liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {
                 // Withdraw any ETH accrued on free floating SLOT from syndicate to this contract
                 // If a partial list of BLS keys that have free floating staked are supplied, then partial funds accrued will be fetched
                 _claimFundsFromSyndicateForDistribution(
-                    liquidStakingNetworkManager.syndicate(),
+                    _liquidStakingNetworkManager.syndicate(),
                     _blsPubKeys
                 );

```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L174-L197
### Syndicate.sol.updateAccruedETHPerShares(): numberOfRegisteredKnots should be cached
```solidity
File: /contracts/syndicate/Syndicate.sol
174:    function updateAccruedETHPerShares() public {

177:        if (numberOfRegisteredKnots > 0) {//@audit: 1st SLOAD

189:            uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);//@audit: 2nd SLOAD
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..e8e3fd0 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -174,7 +174,8 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
     function updateAccruedETHPerShares() public {
         // Ensure there are registered KNOTs. Syndicates are deployed with at least 1 registered but this can fall to zero.
         // Fee recipient should be re-assigned in the event that happens as any further ETH can be collected by owner
-        if (numberOfRegisteredKnots > 0) {
+        uint256 _numberOfRegisteredKnots = numberOfRegisteredKnots;
+        if (_numberOfRegisteredKnots > 0) {
             // All time, total ETH that was earned per slot type (free floating or collateralized)
             uint256 totalEthPerSlotType = calculateETHForFreeFloatingOrCollateralizedHolders();

@@ -186,7 +187,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
                 lastSeenETHPerFreeFloating = totalEthPerSlotType;
             }

-            uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);
+            uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / _numberOfRegisteredKnots);
             accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
             lastSeenETHPerCollateralizedSlotPerKnot = totalEthPerSlotType;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L203-L238
### Syndicate.sol.stake(): use cached value 
```solidity
File: /contracts/syndicate/Syndicate.sol
203:    function stake(bytes[] calldata _blsPubKeys, uint256[] calldata _sETHAmounts, address _onBehalfOf) external {

220:            uint256 totalStaked = sETHTotalStakeForKnot[_blsPubKey];

226:            sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..fcbcc83 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -223,7 +223,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
             if (_sETHAmount + totalStaked > 12 ether) revert InvalidStakeAmount();

             totalFreeFloatingShares += _sETHAmount;
-            sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
+            sETHTotalStakeForKnot[_blsPubKey] =  totalStaked + _sETHAmount;
             sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
             sETHUserClaimForKnot[_blsPubKey][_onBehalfOf] = (_sETHAmount * accumulatedETHPerFreeFloatingShare) / PRECISION;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L491-L536
### Syndicate.sol.\_updateCollateralizedSlotOwnersLiabilitySnapshot():isNoLongerPartOfSyndicate[\_blsPubKey] should be cached 

```solidity
File: /contracts/syndicate/Syndicate.sol
491:    function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {

500:        if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {//@audit: Initial Load

533:        if (!isActive && !isNoLongerPartOfSyndicate[_blsPubKey]) {//2nd LOAD
534:            _deRegisterKnot(_blsPubKey);
535:        }
536:    }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..ef5bca3 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -497,7 +497,8 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
         (address stakeHouse,,,,,bool isActive) = getStakeHouseUniverse().stakeHouseKnotInfo(_blsPubKey);

         // Assuming that there is unprocessed ETH and the knot is still part of the syndicate
-        if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {
+        bool _isNoLongerPartOfSyndicate = isNoLongerPartOfSyndicate[_blsPubKey];
+        if (unprocessedETHForCurrentKnot > 0 && !_isNoLongerPartOfSyndicate) {
             uint256 currentSlashedAmount = getSlotRegistry().currentSlashedAmountOfSLOTForKnot(_blsPubKey);

             // Don't allocate ETH when the current slashed amount is four. Syndicate will wait until ETH is topped up to claim revenue
@@ -530,7 +531,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S

         // if the knot is no longer active, no further accrual of rewards are possible snapshots are possible but ETH accrued up to that point
         // Basically, under a rage quit or voluntary withdrawal from the beacon chain, the knot kick is auto-propagated to syndicate
-        if (!isActive && !isNoLongerPartOfSyndicate[_blsPubKey]) {
+        if (!isActive && !_isNoLongerPartOfSyndicate) {
             _deRegisterKnot(_blsPubKey);
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L550-L552
### Syndicate.sol.\_calculateNewAccumulatedETHPerFreeFloatingShare(): totalFreeFloatingShares should be cached(happy path)
```solidity
File: /contracts/syndicate/Syndicate.sol
550:    function _calculateNewAccumulatedETHPerFreeFloatingShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) {
551:        return totalFreeFloatingShares > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingShares : 0;
552:    }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L630-L637
### Syndicate.sol.\_getCorrectAccumulatedETHPerFreeFloatingShareForBLSPublicKey(): lastAccumulatedETHPerFreeFloatingShare[\_blsPublicKey] should be cached. Optimize the happy path
```solidity
File: /contracts/syndicate/Syndicate.sol
630:    function _getCorrectAccumulatedETHPerFreeFloatingShareForBLSPublicKey(

634:        lastAccumulatedETHPerFreeFloatingShare[_blsPublicKey] > 0 ?
635:        lastAccumulatedETHPerFreeFloatingShare[_blsPublicKey] :
636:        accumulatedETHPerFreeFloatingShare;
637:    }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..9b1e1ed 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -630,9 +630,10 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
     function _getCorrectAccumulatedETHPerFreeFloatingShareForBLSPublicKey(
         bytes memory _blsPublicKey
     ) internal view returns (uint256) {
+        uint256 _lastAccumulatedETHPerFreeFloatingShare = lastAccumulatedETHPerFreeFloatingShare[_blsPublicKey];
         return
-        lastAccumulatedETHPerFreeFloatingShare[_blsPublicKey] > 0 ?
-        lastAccumulatedETHPerFreeFloatingShare[_blsPublicKey] :
+        _lastAccumulatedETHPerFreeFloatingShare > 0 ?
+        _lastAccumulatedETHPerFreeFloatingShare :
         accumulatedETHPerFreeFloatingShare;
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L239-L246
### LiquidStakingManager.sol.updateDAOAddress(): dao should be cached(happy path)
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
239:    function updateDAOAddress(address _newAddress) external onlyDAO {
240:        require(_newAddress != address(0), "Zero address");
241:        require(_newAddress != dao, "Same address");

243:        emit UpdateDAOAddress(dao, _newAddress);

245:        dao = _newAddress;
246:    }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L289-L303
### LiquidStakingManager.sol.rotateEOARepresentative(): smartWalletRepresentative[smartWallet] should be cached(happy path)
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
289:    function rotateEOARepresentative(address _newRepresentative) external {

296:        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

299:        _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..28e07b6 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -293,10 +293,11 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         address smartWallet = smartWalletOfNodeRunner[msg.sender];
         require(smartWallet != address(0), "No smart wallet");
         require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
-        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");
+        address _smartWalletRepresentative = smartWalletRepresentative[smartWallet];
+        require(_smartWalletRepresentative != _newRepresentative, "Invalid rotation to same EOA");

         // unauthorize old representative
-        _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);
+        _authorizeRepresentative(smartWallet, _smartWalletRepresentative, false);

         // authorize new representative
         _authorizeRepresentative(smartWallet, _newRepresentative, true);
```


https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L308-L321
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
308:    function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO {

314:        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

317:        _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..0cab0f9 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -311,10 +311,11 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         address smartWallet = smartWalletOfNodeRunner[_nodeRunner];
         require(smartWallet != address(0), "No smart wallet");
         require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
-        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");
+        address _smartWalletRepresentative = smartWalletRepresentative[smartWallet];
+        require(_smartWalletRepresentative != _newRepresentative, "Invalid rotation to same EOA");

         // unauthorize old representative
-        _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);
+        _authorizeRepresentative(smartWallet, _smartWalletRepresentative, false);

         // authorize new representative
         _authorizeRepresentative(smartWallet, _newRepresentative, true);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L426-L492
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
426:    function registerBLSPublicKeys(

454:        if(smartWalletRepresentative[smartWallet] != address(0)) {
455:            require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
456:        }
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..0b0bfc9 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -451,8 +451,9 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         }

         // Ensure that the node runner does not whitelist multiple EOA representatives - they can only have 1 active at a time
-        if(smartWalletRepresentative[smartWallet] != address(0)) {
-            require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
+        address _smartWalletRepresentative = smartWalletRepresentative[smartWallet];
+        if(_smartWalletRepresentative != address(0)) {
+            require(_smartWalletRepresentative == _eoaRepresentative, "Different EOA specified - rotate outside");
         }

         {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L695-L736
### LiquidStakingManager.sol.\_authorizeRepresentative():smartWalletRepresentative[\_smartWallet] should be cached
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
695:    function _authorizeRepresentative(
    
700:        if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) 

716:        }
717:        else if(_isEnabled && smartWalletRepresentative[_smartWallet] == address(0)) {
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..e276c75 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -697,7 +697,8 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         address _eoaRepresentative,
         bool _isEnabled
     ) internal {
-        if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) {
+        address _smartWalletRepresentative = smartWalletRepresentative[_smartWallet];
+        if(!_isEnabled && _smartWalletRepresentative != address(0)) {

             // authorize the EOA representative on the Stakehouse
             IOwnableSmartWallet(_smartWallet).execute(
@@ -714,7 +715,7 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc

             emit RepresentativeRemoved(_smartWallet, _eoaRepresentative);
         }
-        else if(_isEnabled && smartWalletRepresentative[_smartWallet] == address(0)) {
+        else if(_isEnabled && _smartWalletRepresentative == address(0)) {

             // authorize the EOA representative on the Stakehouse
             IOwnableSmartWallet(_smartWallet).execute(
```


https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L776-L813
### LiquidStakingManager.sol.\_joinLSDNStakehouse(): brand should be cached
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
776:    function _joinLSDNStakehouse(

789:        string memory lowerTicker = IBrandNFT(brand).toLowerCase(stakehouseTicker);
790:        IOwnableSmartWallet(associatedSmartWallet).execute(
791:            address(getTransactionRouter()),
792:            abi.encodeWithSelector(
793:                ITransactionRouter.joinStakehouse.selector,
794:                associatedSmartWallet,
795:                _blsPubKey,
796:                stakehouse,
797:                IBrandNFT(brand).lowercaseBrandTickerToTokenId(lowerTicker),
798:                savETHVault.indexOwnedByTheVault(),
799:                _beaconChainBalanceReport,
800:                _reportSignature
801:            )
802:        );
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..b633cff 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -786,7 +786,8 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         address associatedSmartWallet = smartWalletOfKnot[_blsPubKey];

         // Join the LSDN stakehouse
-        string memory lowerTicker = IBrandNFT(brand).toLowerCase(stakehouseTicker);
+        address _brand = brand;
+        string memory lowerTicker = IBrandNFT(_brand).toLowerCase(stakehouseTicker);
         IOwnableSmartWallet(associatedSmartWallet).execute(
             address(getTransactionRouter()),
             abi.encodeWithSelector(
@@ -794,7 +795,7 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
                 associatedSmartWallet,
                 _blsPubKey,
                 stakehouse,
-                IBrandNFT(brand).lowercaseBrandTickerToTokenId(lowerTicker),
+                IBrandNFT(_brand).lowercaseBrandTickerToTokenId(lowerTicker),
                 savETHVault.indexOwnedByTheVault(),
                 _beaconChainBalanceReport,
                 _reportSignature
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L854-L856
### LiquidStakingManager.sol.\_createLSDNStakehouse(): gatekeeper should be cached
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
854:        if (address(gatekeeper) != address(0)) {
855:            IStakeHouseRegistry(stakehouse).setGateKeeper(address(gatekeeper));
856:        }
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..5683a47 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -850,9 +850,9 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
                 address(this)
             )
         );
-
-        if (address(gatekeeper) != address(0)) {
-            IStakeHouseRegistry(stakehouse).setGateKeeper(address(gatekeeper));
+        OptionalHouseGatekeeper  _gatekeeper = gatekeeper;
+        if (address(_gatekeeper) != address(0)) {
+            IStakeHouseRegistry(stakehouse).setGateKeeper(address(_gatekeeper));
         }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L816-L876
### LiquidStakingManager.sol.\_createLSDNStakehouse(): stakehouse should be cached
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
816:    function _createLSDNStakehouse(

842:        stakehouse = getStakeHouseUniverse().memberKnotToStakeHouse(_blsPublicKeyOfKnot);
843:        IERC20 sETH = IERC20(getSlotRegistry().stakeHouseShareTokens(stakehouse));

846:        IOwnableSmartWallet(associatedSmartWallet).execute(
847:            stakehouse,
848:            abi.encodeWithSelector(
849:                Ownable.transferOwnership.selector,
850:                address(this)
851:            )
852:        );


854:        if (address(gatekeeper) != address(0)) {
855:            IStakeHouseRegistry(stakehouse).setGateKeeper(address(gatekeeper));
856:        }

875:        emit StakehouseCreated(stakehouseTicker, stakehouse);
876:    }
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..3ff2ec5 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -840,11 +840,12 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc

         // Capture the address of the Stakehouse for future knots to join
         stakehouse = getStakeHouseUniverse().memberKnotToStakeHouse(_blsPublicKeyOfKnot);
-        IERC20 sETH = IERC20(getSlotRegistry().stakeHouseShareTokens(stakehouse));
+        address _stakehouse = stakehouse;
+        IERC20 sETH = IERC20(getSlotRegistry().stakeHouseShareTokens(_stakehouse));

         // Give liquid staking manager ability to manage keepers and set a house keeper if decided by the network
         IOwnableSmartWallet(associatedSmartWallet).execute(
-            stakehouse,
+            _stakehouse,
             abi.encodeWithSelector(
                 Ownable.transferOwnership.selector,
                 address(this)
@@ -852,7 +853,7 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         );

         if (address(gatekeeper) != address(0)) {
-            IStakeHouseRegistry(stakehouse).setGateKeeper(address(gatekeeper));
+            IStakeHouseRegistry(_stakehouse).setGateKeeper(address(gatekeeper));
         }

         // Deploy the EIP1559 transaction reward sharing contract but no priority required because sETH will be auto staked
@@ -872,7 +873,7 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         // Auto-stake sETH by pulling sETH out the smart wallet and staking in the syndicate
         _autoStakeWithSyndicate(associatedSmartWallet, _blsPublicKeyOfKnot);

-        emit StakehouseCreated(stakehouseTicker, stakehouse);
+        emit StakehouseCreated(stakehouseTicker, _stakehouse);
     }

     /// @dev Remove the sETH from the node runner smart wallet in order to auto-stake the sETH in the syndicate
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L921-L931
### LiquidStakingManager.sol.\_calculateCommission(): daoCommissionPercentage should be cached
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
921:    function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {

924:        if (daoCommissionPercentage > 0) {
925:            uint256 daoAmount = (_received * daoCommissionPercentage) / MODULO;
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..ebcc445 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -921,8 +921,10 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
     function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {
         require(_received > 0, "Nothing received");

-        if (daoCommissionPercentage > 0) {
-            uint256 daoAmount = (_received * daoCommissionPercentage) / MODULO;
+        uint256 _daoCommissionPercentage = daoCommissionPercentage;
+
+        if (_daoCommissionPercentage > 0) {
+            uint256 daoAmount = (_received * _daoCommissionPercentage) / MODULO;
             uint256 rest = _received - daoAmount;
             return (rest, daoAmount);
         }
```

### The result of a function call should be cached rather than re-calling the function

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWallet.sol#L94-L111
### OwnableSmartWallet.sol.transferOwnership(): owner() should be cached
```solidity
File: /contracts/smart-wallet/OwnableSmartWallet.sol
94:    function transferOwnership(address newOwner)
    
100:        require(
101:            isTransferApproved(owner(), msg.sender),
102:            "OwnableSmartWallet: Transfer is not allowed"
103:        ); // F: [OSW-4]

107:        if (msg.sender != owner()) {
108:            _setApproval(owner(), msg.sender, false); // F: [OSW-5]
109:        }
```

```diff
diff --git a/contracts/smart-wallet/OwnableSmartWallet.sol b/contracts/smart-wallet/OwnableSmartWallet.sol
index aec7d9e..e11f678 100644
--- a/contracts/smart-wallet/OwnableSmartWallet.sol
+++ b/contracts/smart-wallet/OwnableSmartWallet.sol
@@ -97,15 +97,16 @@ contract OwnableSmartWallet is IOwnableSmartWallet, Ownable, Initializable {
     {
         // Only the owner themselves or an address that is approved for transfers
         // is authorized to do this
+        address _owner = owner();
         require(
-            isTransferApproved(owner(), msg.sender),
+            isTransferApproved(_owner, msg.sender),
             "OwnableSmartWallet: Transfer is not allowed"
         ); // F: [OSW-4]

         // Approval is revoked, in order to avoid unintended transfer allowance
         // if this wallet ever returns to the previous owner
-        if (msg.sender != owner()) {
-            _setApproval(owner(), msg.sender, false); // F: [OSW-5]
+        if (msg.sender != _owner) {
+            _setApproval(_owner, msg.sender, false); // F: [OSW-5]
         }
         _transferOwnership(newOwner); // F: [OSW-5]
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L66-L109
### GiantSavETHVaultPool.sol.withdrawDETH(): getDETH() should be cached
```solidity
File: /contracts/liquid-staking/GiantSavETHVaultPool.sol
66:    function withdrawDETH(

77:        uint256 dETHReceivedFromAllSavETHVaults = getDETH().balanceOf(address(this));//@audit: getDETH() initial call

105:        dETHReceivedFromAllSavETHVaults = getDETH().balanceOf(address(this)) - dETHReceivedFromAllSavETHVaults; //@audit: getDETH() second call

108:        getDETH().transfer(msg.sender, dETHReceivedFromAllSavETHVaults);//@audit: getDETH() third call
    }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L126-L194
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
126:    function burnLPToken(LPToken _lpToken, uint256 _amount) public nonReentrant returns (uint256) {

158:                uint256 dETHBalance = getSavETHRegistry().knotDETHBalanceInIndex(indexOwnedByTheVault, blsPublicKeyOfKnot);
159:                uint256 savETHBalance = getSavETHRegistry().dETHToSavETH(dETHBalance);

165:  getSavETHRegistry().addKnotToOpenIndex(liquidStakingManager.stakehouse(), blsPublicKeyOfKnot, address(this));

177:            getSavETHRegistry().withdraw(msg.sender, uint128(redemptionValue));

179:            uint256 dETHRedeemed = getSavETHRegistry().savETHToDETH(redemptionValue);

```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L199-L233
### StakingFundsVault.sol.claimRewards(): liquidStakingNetworkManager should be cached
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
199:    function claimRewards(

215:            if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {//@audit:1st call liquidStakingNetworkManager.syndicate()

218:                _claimFundsFromSyndicateForDistribution(
219:                   liquidStakingNetworkManager.syndicate(),//@audit: 2nd call
220:                   _blsPubKeys
221:                );
```

```diff
diff --git a/contracts/liquid-staking/StakingFundsVault.sol b/contracts/liquid-staking/StakingFundsVault.sol
index ecc185c..3338ced 100644
--- a/contracts/liquid-staking/StakingFundsVault.sol
+++ b/contracts/liquid-staking/StakingFundsVault.sol
@@ -211,12 +211,13 @@ contract StakingFundsVault is
                 getAccountManager().blsPublicKeyToLifecycleStatus(_blsPubKeys[i]) == IDataStructures.LifecycleStatus.TOKENS_MINTED,
                 "Derivatives not minted"
             );
+            address _syndicate = liquidStakingNetworkManager.syndicate();

-            if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {
+            if (i == 0 && !Syndicate(payable(_syndicate)).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {
                 // Withdraw any ETH accrued on free floating SLOT from syndicate to this contract
                 // If a partial list of BLS keys that have free floating staked are supplied, then partial funds accrued will be fetched
                 _claimFundsFromSyndicateForDistribution(
-                    liquidStakingNetworkManager.syndicate(),
+                    _syndicate,
                     _blsPubKeys
                 );
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L401-L438
### Syndicate.sol.previewUnclaimedETHAsCollateralizedSlotOwner(): getSlotRegistry() should be cached(Called 4 times)
```solidity
File: /contracts/syndicate/Syndicate.sol
401:    function previewUnclaimedETHAsCollateralizedSlotOwner(

415:        uint256 currentSlashedAmount = getSlotRegistry().currentSlashedAmountOfSLOTForKnot(_blsPubKey);
416:        uint256 numberOfCollateralisedSlotOwnersForKnot = getSlotRegistry().numberOfCollateralisedSlotOwnersForKnot(_blsPubKey);
417:        (address stakeHouse,,,,,) = getStakeHouseUniverse().stakeHouseKnotInfo(_blsPubKey);

420:        for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
421:            address collateralizedOwnerAtIndex = getSlotRegistry().getCollateralisedOwnerAtIndex(_blsPubKey, i);
422:            if (collateralizedOwnerAtIndex == _staker) {
423:                uint256 balance = getSlotRegistry().totalUserCollateralisedSLOTBalanceForKnot(
424:                    stakeHouse,
425:                    collateralizedOwnerAtIndex,
426:                    _blsPubKey
427:                );
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L491-L536
### Syndicate.sol.\_updateCollateralizedSlotOwnersLiabilitySnapshot():getSlotRegistry() should be called 
```solidity
File: /contracts/syndicate/Syndicate.sol
491:    function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {

501:            uint256 currentSlashedAmount = getSlotRegistry().currentSlashedAmountOfSLOTForKnot(_blsPubKey);

504:            if (currentSlashedAmount < 4 ether) {

506:                uint256 numberOfCollateralisedSlotOwnersForKnot = getSlotRegistry().numberOfCollateralisedSlotOwnersForKnot(_blsPubKey);

510:                    address collateralizedOwnerAtIndex = getSlotRegistry().getCollateralisedOwnerAtIndex(_blsPubKey, 0);

513:                    for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
514:                        address collateralizedOwnerAtIndex = getSlotRegistry().getCollateralisedOwnerAtIndex(_blsPubKey, i);
515:                        uint256 balance = getSlotRegistry().totalUserCollateralisedSLOTBalanceForKnot(
516:                            stakeHouse,
517:                            collateralizedOwnerAtIndex,
518:                            _blsPubKey
519:                        );
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L555-L580
### Syndicate.sol.\_registerKnotsToSyndicate(): getSlotRegistry() should be cached
```solidity
File: /contracts/syndicate/Syndicate.sol
555:    function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots) internal {

573:            uint256 numberOfCollateralisedSlotOwnersForKnot = getSlotRegistry().numberOfCollateralisedSlotOwnersForKnot(blsPubKey);

575:           if (getSlotRegistry().currentSlashedAmountOfSLOTForKnot(blsPubKey) != 0) revert InvalidNumberOfCollateralizedOwners();

```
### Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L684
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
684:    function _isNodeRunnerValid(address _nodeRunner) internal view returns (bool) {

739:    function _stake(
740:        bytes calldata _blsPublicKey,
741:        bytes calldata _cipherText,
742:        bytes calldata _aesEncryptorKey,
743:        IDataStructures.EIP712Signature calldata _encryptionSignature,
744:        bytes32 dataRoot
745:    ) internal {


776:    function _joinLSDNStakehouse(
777:        bytes calldata _blsPubKey,
778:        IDataStructures.ETH2DataReport calldata _beaconChainBalanceReport,
779:        IDataStructures.EIP712Signature calldata _reportSignature
780:    ) internal {


816:    function _createLSDNStakehouse(
817:        bytes calldata _blsPublicKeyOfKnot,
818:        IDataStructures.ETH2DataReport calldata _beaconChainBalanceReport,
819:        IDataStructures.EIP712Signature calldata _reportSignature
820:    ) internal {

934:    function _assertEtherIsReadyForValidatorStaking(bytes calldata blsPubKey) internal view {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L597
```solidity
File: /contracts/syndicate/Syndicate.sol
597:    function _deRegisterKnots(bytes[] calldata _blsPublicKeys) internal {
```

### Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L245-L280
### Syndicate.sol.unstake(): sETHStakedBalanceForKnot[\_blsPubKey][msg.sender] should be cached

```solidity
File: /contracts/syndicate/Syndicate.sol
245:    function unstake(

259:        for (uint256 i; i < _blsPubKeys.length; ++i) {

262:            if (sETHStakedBalanceForKnot[_blsPubKey][msg.sender] < _sETHAmount) revert NothingStaked();//@audit: Called here

273:            sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;//@audit: Another call
```


https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L278-L284
### LiquidStakingManager.sol.updateNodeRunnerWhitelistStatus(): isNodeRunnerWhitelisted[\_nodeRunner] should be cached
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
278:    function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {
279:        require(_nodeRunner != address(0), "Zero address");
280:        require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

282:        isNodeRunnerWhitelisted[_nodeRunner] = isWhitelisted;
283:        emit NodeRunnerWhitelistingStatusChanged(_nodeRunner, isWhitelisted);
284:    }
```

### Use calldata instead of memory for function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L355-L357
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
355:    function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {

356:_claimFundsFromSyndicateForDistribution(liquidStakingNetworkManager.syndicate(), _blsPubKeys);
357:    }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L360-L368
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
360:    function _claimFundsFromSyndicateForDistribution(address _syndicate, bytes[] memory _blsPubKeys) internal {
361:        require(_syndicate != address(0), "Invalid configuration");

363:        // Claim all of the ETH due from the syndicate for the auto-staked sETH
364:        Syndicate syndicateContract = Syndicate(payable(_syndicate));
365:        syndicateContract.claimAsStaker(address(this), _blsPubKeys);


367:        updateAccumulatedETHPerLP();
368:    }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L337-L339
```solidity
File: /contracts/syndicate/Syndicate.sol
337:    function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {
338:        _updateCollateralizedSlotOwnersLiabilitySnapshot(_blsPubKey);
339:    }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..9477d10 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -334,7 +334,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S

     /// @notice For any new ETH received by the syndicate, at the knot level allocate ETH owed to each collateralized owner
     /// @param _blsPubKey BLS public key relating to the collateralized owners that need updating
-    function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {
+    function updateCollateralizedSlotOwnersAccruedETH(bytes calldata _blsPubKey) external {
         _updateCollateralizedSlotOwnersLiabilitySnapshot(_blsPubKey);
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L343-L349
```solidity
File: /contracts/syndicate/Syndicate.sol
343:    function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {
344:        uint256 numOfKeys = _blsPubKeys.length;
345:        if (numOfKeys == 0) revert EmptyArray();
346:        for (uint256 i; i < _blsPubKeys.length; ++i) {
347:            _updateCollateralizedSlotOwnersLiabilitySnapshot(_blsPubKeys[i]);
348:        }
349:    }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..beb0a00 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -340,7 +340,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S

     /// @notice For any new ETH received by the syndicate, at the knot level allocate ETH owed to each collateralized owner and do it for a batch of knots
     /// @param _blsPubKeys List of BLS public keys related to the collateralized owners that need updating
-    function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {
+    function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] calldata _blsPubKeys) external {
         uint256 numOfKeys = _blsPubKeys.length;
         if (numOfKeys == 0) revert EmptyArray();
         for (uint256 i; i < _blsPubKeys.length; ++i) {
```

### Emitting storage values instead of the memory one.
Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L267-L273
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
267:    function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {
268:        require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
269:        enableWhitelisting = _changeWhitelist;
270:        emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);

272:        return enableWhitelisting;
273:    }
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..d98fe71 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -267,9 +267,9 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
     function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {
         require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
         enableWhitelisting = _changeWhitelist;
-        emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);
+        emit WhitelistingStatusChanged(msg.sender, _changeWhitelist);

-        return enableWhitelisting;
+        return _changeWhitelist;
     }
```

### Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L131
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
131:        bytes memory blsPublicKeyOfKnot = KnotAssociatedWithLPToken[_lpToken];
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L295
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
295:        bytes memory associatedBLSPublicKeyOfLpToken = KnotAssociatedWithLPToken[_token];
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L319
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
319:            bytes memory blsPubKey = KnotAssociatedWithLPToken[token];
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L85-L86
```solidity
File: /contracts/liquid-staking/ETHPoolLPFactory.sol
85:        bytes memory blsPublicKeyOfPreviousKnot = KnotAssociatedWithLPToken[_oldLPToken];
86:        bytes memory blsPublicKeyOfNewKnot = KnotAssociatedWithLPToken[_newLPToken];
```

### x += y costs more gas than x = x + y for state variables

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L39
```solidity
File: /contracts/liquid-staking/GiantPoolBase.sol
39:        idleETH += msg.value;
```

```diff
diff --git a/contracts/liquid-staking/GiantPoolBase.sol b/contracts/liquid-staking/GiantPoolBase.sol
index 8a8ff70..a1bddce 100644
--- a/contracts/liquid-staking/GiantPoolBase.sol
+++ b/contracts/liquid-staking/GiantPoolBase.sol
@@ -36,7 +36,7 @@ contract GiantPoolBase is ReentrancyGuard {
         require(msg.value == _amount, "Value equal to amount");

         // The ETH capital has not yet been deployed to a liquid staking network
-        idleETH += msg.value;
+        idleETH = idleETH + msg.value;

         // Mint giant LP at ratio of 1:1
         lpTokenETH.mint(msg.sender, msg.value);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L57
```solidity
File: /contracts/liquid-staking/GiantPoolBase.sol
57:        idleETH -= _amount;
```

```diff
diff --git a/contracts/liquid-staking/GiantPoolBase.sol b/contracts/liquid-staking/GiantPoolBase.sol
index 8a8ff70..fa6296d 100644
--- a/contracts/liquid-staking/GiantPoolBase.sol
+++ b/contracts/liquid-staking/GiantPoolBase.sol
@@ -54,7 +54,7 @@ contract GiantPoolBase is ReentrancyGuard {
         require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");
         require(idleETH >= _amount, "Come back later or withdraw less ETH");

-        idleETH -= _amount;
+        idleETH = idleETH - _amount;

         lpTokenETH.burn(msg.sender, _amount);
         (bool success,) = msg.sender.call{value: _amount}("");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L46
```solidity
File: /contracts/liquid-staking/GiantSavETHVaultPool.sol
46:            idleETH -= transactionAmount;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L40
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
40:            idleETH -= _ETHTransactionAmounts[i];
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L58
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
58:        totalShares += 4 ether;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L185
```solidity
File: /contracts/syndicate/Syndicate.sol
185:                accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);

190:            accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;

225:            totalFreeFloatingShares += _sETHAmount;

269:            totalFreeFloatingShares -= _sETHAmount;

317:            totalClaimed += unclaimedUserShare;

558:        numberOfRegisteredKnots += knotsToRegister;

621:        totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];

624:        numberOfRegisteredKnots -= 1;

658:        totalClaimed += unclaimedUserShare;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L782
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
782:        numberOfKnots += 1;

839:        numberOfKnots += 1;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L65
```solidity
File: /contracts/liquid-staking/SyndicateRewardsProcessor.sol
65:                totalClaimed += due;

85:                accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;
```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L431
```solidity
File: /contracts/syndicate/Syndicate.sol
431:                        balance * unprocessedForKnot / (4 ether - currentSlashedAmount);
```

The operation `4 ether - currentSlashedAmount` cannot underflow due to the check on [Line 429](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L429) that ensures that `currentSlashedAmount` is less than `4 ether` before performing the subtraction

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L522
```solidity
File: /contracts/syndicate/Syndicate.sol
522:                            balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);
```

The operation `4 ether - currentSlashedAmount` cannot underflow due to the check on [Line 504](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L504) that ensures that `currentSlashedAmount` is less than `4 ether` 

### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

**Affected code**
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L76-L89
```solidity
File: /contracts/liquid-staking/GiantPoolBase.sol
76:        for (uint256 i; i < amountOfTokens; ++i) {
```

```diff
diff --git a/contracts/liquid-staking/GiantPoolBase.sol b/contracts/liquid-staking/GiantPoolBase.sol
index 8a8ff70..2b68c0d 100644
--- a/contracts/liquid-staking/GiantPoolBase.sol
+++ b/contracts/liquid-staking/GiantPoolBase.sol
@@ -73,7 +73,7 @@ contract GiantPoolBase is ReentrancyGuard {

         _onWithdraw(_lpTokens);

-        for (uint256 i; i < amountOfTokens; ++i) {
+        for (uint256 i; i < amountOfTokens;) {
             LPToken token = _lpTokens[i];
             uint256 amount = _amounts[i];

@@ -86,6 +86,10 @@ contract GiantPoolBase is ReentrancyGuard {
             token.transfer(msg.sender, amount);

             emit LPSwappedForVaultLP(address(token), msg.sender, amount);
+
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L42-L59
```solidity
File: /contracts/liquid-staking/GiantSavETHVaultPool.sol
42:        for (uint256 i; i < numOfSavETHVaults; ++i) {
```

```diff
diff --git a/contracts/liquid-staking/GiantSavETHVaultPool.sol b/contracts/liquid-staking/GiantSavETHVaultPool.sol
index 4edbd43..6d211c2 100644
--- a/contracts/liquid-staking/GiantSavETHVaultPool.sol
+++ b/contracts/liquid-staking/GiantSavETHVaultPool.sol
@@ -39,7 +39,7 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {
         require(numOfSavETHVaults == _stakeAmounts.length, "Inconsistent array lengths");

         // For every vault specified, supply ETH for at least 1 BLS public key of a LSDN validator
-        for (uint256 i; i < numOfSavETHVaults; ++i) {
+        for (uint256 i; i < numOfSavETHVaults;) {
             uint256 transactionAmount = _ETHTransactionAmounts[i];

             // As ETH is being deployed to a savETH pool vault, it is no longer idle
@@ -56,6 +56,9 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {
                 _blsPublicKeys[i],
                 _stakeAmounts[i]
             );
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L78-L102
```solidity
File: /contracts/liquid-staking/GiantSavETHVaultPool.sol
        for (uint256 i; i < numOfVaults; ++i) {

            for (uint256 j; j < _lpTokens[i].length; ++j) {

            }

        }
```

```diff
diff --git a/contracts/liquid-staking/GiantSavETHVaultPool.sol b/contracts/liquid-staking/GiantSavETHVaultPool.sol
index 4edbd43..807a07d 100644
--- a/contracts/liquid-staking/GiantSavETHVaultPool.sol
+++ b/contracts/liquid-staking/GiantSavETHVaultPool.sol
@@ -75,14 +75,13 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {

         // Firstly capture current dETH balance and see how much has been deposited after the loop
         uint256 dETHReceivedFromAllSavETHVaults = getDETH().balanceOf(address(this));
+    unchecked{
         for (uint256 i; i < numOfVaults; ++i) {
             SavETHVault vault = SavETHVault(_savETHVaults[i]);
-
             // Simultaneously check the status of LP tokens held by the vault and the giant LP balance of the user
             for (uint256 j; j < _lpTokens[i].length; ++j) {
                 LPToken token = _lpTokens[i][j];
                 uint256 amount = _amounts[i][j];
-
                 // Check the user has enough giant LP to burn and that the pool has enough savETH vault LP
                 _assertUserHasEnoughGiantLPToClaimVaultLP(token, amount);

@@ -100,7 +99,7 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {
             // Ask
             vault.burnLPTokens(_lpTokens[i], _amounts[i]);
         }
-
+    }
         // Calculate how much dETH has been received from burning
         dETHReceivedFromAllSavETHVaults = getDETH().balanceOf(address(this)) - dETHReceivedFromAllSavETHVaults;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L128-L130
```solidity
File: /contracts/liquid-staking/GiantSavETHVaultPool.sol
128:        for (uint256 i; i < numOfRotations; ++i) {
129:            SavETHVault(_savETHVaults[i]).batchRotateLPTokens(_oldLPTokens[i], _newLPTokens[i], _amounts[i]);
130:        }
```

```diff
diff --git a/contracts/liquid-staking/GiantSavETHVaultPool.sol b/contracts/liquid-staking/GiantSavETHVaultPool.sol
index 4edbd43..31fe07d 100644
--- a/contracts/liquid-staking/GiantSavETHVaultPool.sol
+++ b/contracts/liquid-staking/GiantSavETHVaultPool.sol
@@ -125,8 +125,11 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {
         require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
         require(numOfRotations == _amounts.length, "Inconsistent arrays");
         require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
-        for (uint256 i; i < numOfRotations; ++i) {
+        for (uint256 i; i < numOfRotations;) {
             SavETHVault(_savETHVaults[i]).batchRotateLPTokens(_oldLPTokens[i], _newLPTokens[i], _amounts[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L146-L156
```solidity
File: /contracts/liquid-staking/GiantSavETHVaultPool.sol
146:        for (uint256 i; i < numOfVaults; ++i) {
147:            SavETHVault vault = SavETHVault(_savETHVaults[i]);
148:            for (uint256 j; j < _lpTokens[i].length; ++j) {

153:            }

156:        }
```

```diff
diff --git a/contracts/liquid-staking/GiantSavETHVaultPool.sol b/contracts/liquid-staking/GiantSavETHVaultPool.sol
index 4edbd43..a600aeb 100644
--- a/contracts/liquid-staking/GiantSavETHVaultPool.sol
+++ b/contracts/liquid-staking/GiantSavETHVaultPool.sol
@@ -143,6 +143,8 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {
         require(numOfVaults > 0, "Empty arrays");
         require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
         require(numOfVaults == _amounts.length, "Inconsistent arrays");
+
+        unchecked {
         for (uint256 i; i < numOfVaults; ++i) {
             SavETHVault vault = SavETHVault(_savETHVaults[i]);
             for (uint256 j; j < _lpTokens[i].length; ++j) {
@@ -154,5 +156,7 @@ contract GiantSavETHVaultPool is StakehouseAPI, GiantPoolBase {

             vault.burnLPTokens(_lpTokens[i], _amounts[i]);
         }
+        }
+
     }
 }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L63-L73
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
63:        for (uint256 i; i < numOfValidators; ++i) {

73:        }
```

```diff
diff --git a/contracts/liquid-staking/SavETHVault.sol b/contracts/liquid-staking/SavETHVault.sol
index 576afb9..6950212 100644
--- a/contracts/liquid-staking/SavETHVault.sol
+++ b/contracts/liquid-staking/SavETHVault.sol
@@ -60,7 +60,7 @@ contract SavETHVault is Initializable, ETHPoolLPFactory, ReentrancyGuard {
         require(numOfValidators == _amounts.length, "Inconsistent array lengths");

         uint256 totalAmount;
-        for (uint256 i; i < numOfValidators; ++i) {
+        for (uint256 i; i < numOfValidators;) {
             require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
             require(
                 getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
@@ -70,6 +70,9 @@ contract SavETHVault is Initializable, ETHPoolLPFactory, ReentrancyGuard {
             uint256 amount = _amounts[i];
             totalAmount += amount;
             _depositETHForStaking(_blsPublicKeyOfKnots[i], amount, false);
+            unchecked {
+                ++i;
+            }
         }

         // Ensure that the sum of LP tokens issued equals the ETH deposited into the contract
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L103-L106
```solidity
File:/contracts/liquid-staking/SavETHVault.sol
103:        for (uint256 i; i < numOfTokens; ++i) {
104:            LPToken token = lpTokenForKnot[_blsPublicKeys[i]];
105:            burnLPToken(token, _amounts[i]);
106:        }
```

```diff
diff --git a/contracts/liquid-staking/SavETHVault.sol b/contracts/liquid-staking/SavETHVault.sol
index 576afb9..b03dbd2 100644
--- a/contracts/liquid-staking/SavETHVault.sol
+++ b/contracts/liquid-staking/SavETHVault.sol
@@ -100,9 +100,12 @@ contract SavETHVault is Initializable, ETHPoolLPFactory, ReentrancyGuard {
         uint256 numOfTokens = _blsPublicKeys.length;
         require(numOfTokens > 0, "Empty arrays");
         require(numOfTokens == _amounts.length, "Inconsistent array length");
-        for (uint256 i; i < numOfTokens; ++i) {
+        for (uint256 i; i < numOfTokens;) {
             LPToken token = lpTokenForKnot[_blsPublicKeys[i]];
             burnLPToken(token, _amounts[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L116-L118
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
116:        for (uint256 i; i < numOfTokens; ++i) {
117:            burnLPToken(_lpTokens[i], _amounts[i]);
118:        }
```

```diff
diff --git a/contracts/liquid-staking/SavETHVault.sol b/contracts/liquid-staking/SavETHVault.sol
index 576afb9..2517ad3 100644
--- a/contracts/liquid-staking/SavETHVault.sol
+++ b/contracts/liquid-staking/SavETHVault.sol
@@ -113,8 +113,11 @@ contract SavETHVault is Initializable, ETHPoolLPFactory, ReentrancyGuard {
         uint256 numOfTokens = _lpTokens.length;
         require(numOfTokens > 0, "Empty arrays");
         require(numOfTokens == _amounts.length, "Inconsisent array length");
-        for (uint256 i; i < numOfTokens; ++i) {
+        for (uint256 i; i < numOfTokens;) {
             burnLPToken(_lpTokens[i], _amounts[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L38-L52
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
38:        for (uint256 i; i < numOfVaults; ++i) {

52:        }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..23bfa31 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -35,7 +35,7 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
         require(numOfVaults > 0, "Zero vaults");
         require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");
         require(numOfVaults == _amounts.length, "Inconsistent lengths");
-        for (uint256 i; i < numOfVaults; ++i) {
+        for (uint256 i; i < numOfVaults;) {
             // As ETH is being deployed to a staking funds vault, it is no longer idle
             idleETH -= _ETHTransactionAmounts[i];

@@ -49,6 +49,9 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
                 _blsPublicKeyOfKnots[i],
                 _amounts[i]
             );
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L64-L69
```solidity
File:/contracts/liquid-staking/GiantMevAndFeesPool.sol
64:        for (uint256 i; i < numOfVaults; ++i) {

69:        }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..57a4586 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -61,11 +61,14 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
         uint256 numOfVaults = _stakingFundsVaults.length;
         require(numOfVaults > 0, "Empty array");
         require(numOfVaults == _blsPublicKeysForKnots.length, "Inconsistent array lengths");
-        for (uint256 i; i < numOfVaults; ++i) {
+        for (uint256 i; i < numOfVaults;) {
             StakingFundsVault(payable(_stakingFundsVaults[i])).claimRewards(
                 address(this),
                 _blsPublicKeysForKnots[i]
             );
+            unchecked {
+                ++i;
+            }
         }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L90-L95
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
90:        for (uint256 i; i < _stakingFundsVaults.length; ++i) {

95:        }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..ccfbb42 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -87,11 +87,14 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
         require(_stakingFundsVaults.length == _lpTokens.length, "Inconsistent array lengths");

         uint256 accumulated;
-        for (uint256 i; i < _stakingFundsVaults.length; ++i) {
+        for (uint256 i; i < _stakingFundsVaults.length;) {
             accumulated = StakingFundsVault(payable(_stakingFundsVaults[i])).batchPreviewAccumulatedETH(
                 address(this),
                 _lpTokens[i]
             );
+            unchecked {
+                ++i;
+            }
         }

         return _previewAccumulatedETH(_user, address(lpTokenETH), lpTokenETH.balanceOf(_user), lpTokenETH.totalSupply(), accumulated);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L117-L119
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
117:        for (uint256 i; i < numOfRotations; ++i) {

118:        }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..899245c 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -114,8 +114,11 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
         require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
         require(numOfRotations == _amounts.length, "Inconsistent arrays");
         require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
-        for (uint256 i; i < numOfRotations; ++i) {
+        for (uint256 i; i < numOfRotations;) {
             StakingFundsVault(payable(_stakingFundsVaults[i])).batchRotateLPTokens(_oldLPTokens[i], _newLPTokens[i], _amounts[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L135-L137
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
135:        for (uint256 i; i < numOfVaults; ++i) {

137:        }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..ad267ea 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -132,8 +132,11 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
         require(numOfVaults > 0, "Empty arrays");
         require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
         require(numOfVaults == _amounts.length, "Inconsistent arrays");
-        for (uint256 i; i < numOfVaults; ++i) {
+        for (uint256 i; i < numOfVaults;) {
             StakingFundsVault(payable(_stakingFundsVaults[i])).burnLPTokensForETH(_lpTokens[i], _amounts[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L183-L185
```solidity
File: /contracts/liquid-staking/GiantMevAndFeesPool.sol
183:        for (uint256 i; i < _lpTokens.length; ++i) {

185:        }
```

```diff
diff --git a/contracts/liquid-staking/GiantMevAndFeesPool.sol b/contracts/liquid-staking/GiantMevAndFeesPool.sol
index 0e76efc..4262999 100644
--- a/contracts/liquid-staking/GiantMevAndFeesPool.sol
+++ b/contracts/liquid-staking/GiantMevAndFeesPool.sol
@@ -180,8 +180,11 @@ contract GiantMevAndFeesPool is ITransferHookProcessor, GiantPoolBase, Syndicate
     /// @dev On withdrawing LP in exchange for burning giant LP, claim rewards
     function _onWithdraw(LPToken[] calldata _lpTokens) internal override {
         // Use the transfer hook of LPToken to trigger the claiming accrued ETH
-        for (uint256 i; i < _lpTokens.length; ++i) {
+        for (uint256 i; i < _lpTokens.length;) {
             _lpTokens[i].transfer(address(this), _lpTokens[i].balanceOf(address(this)));
+            unchecked {
+                ++i;
+            }
         }

         _distributeETHRewardsToUserForToken(
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L78-L104
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
78:        for (uint256 i; i < numOfValidators; ++i) {

104:        }
```

```diff
diff --git a/contracts/liquid-staking/StakingFundsVault.sol b/contracts/liquid-staking/StakingFundsVault.sol
index ecc185c..2d2caf7 100644
--- a/contracts/liquid-staking/StakingFundsVault.sol
+++ b/contracts/liquid-staking/StakingFundsVault.sol
@@ -75,7 +75,7 @@ contract StakingFundsVault is
         updateAccumulatedETHPerLP();

         uint256 totalAmount;
-        for (uint256 i; i < numOfValidators; ++i) {
+        for (uint256 i; i < numOfValidators;) {
             require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
             require(
                 getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
@@ -101,6 +101,9 @@ contract StakingFundsVault is
             // Ensure user cannot get historical rewards
             tokenForKnot = lpTokenForKnot[_blsPublicKeyOfKnots[i]];
             claimed[msg.sender][address(tokenForKnot)] = (tokenForKnot.balanceOf(msg.sender) * accumulatedETHPerLPShare) / PRECISION;
+            unchecked {
+                ++i;
+            }
         }

         // Ensure that the sum of LP tokens issued equals the ETH deposited into the contract
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L152-L156
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
152:        for (uint256 i; i < numOfTokens; ++i) {

156:        }
```

```diff
diff --git a/contracts/liquid-staking/StakingFundsVault.sol b/contracts/liquid-staking/StakingFundsVault.sol
index ecc185c..3a822a6 100644
--- a/contracts/liquid-staking/StakingFundsVault.sol
+++ b/contracts/liquid-staking/StakingFundsVault.sol
@@ -149,10 +149,13 @@ contract StakingFundsVault is
         uint256 numOfTokens = _blsPublicKeys.length;
         require(numOfTokens > 0, "Empty arrays");
         require(numOfTokens == _amounts.length, "Inconsistent array length");
-        for (uint256 i; i < numOfTokens; ++i) {
+        for (uint256 i; i < numOfTokens;) {
             LPToken token = lpTokenForKnot[_blsPublicKeys[i]];
             require(address(token) != address(0), "No ETH staked for specified BLS key");
             burnLPForETH(token, _amounts[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L166-L168
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
166:        for (uint256 i; i < numOfTokens; ++i) {
167:            burnLPForETH(_lpTokens[i], _amounts[i]);
168:        }
```

```diff
diff --git a/contracts/liquid-staking/StakingFundsVault.sol b/contracts/liquid-staking/StakingFundsVault.sol
index ecc185c..dabf20a 100644
--- a/contracts/liquid-staking/StakingFundsVault.sol
+++ b/contracts/liquid-staking/StakingFundsVault.sol
@@ -163,8 +163,11 @@ contract StakingFundsVault is
         uint256 numOfTokens = _lpTokens.length;
         require(numOfTokens > 0, "Empty arrays");
         require(numOfTokens == _amounts.length, "Inconsistent array length");
-        for (uint256 i; i < numOfTokens; ++i) {
+        for (uint256 i; i < numOfTokens;) {
             burnLPForETH(_lpTokens[i], _amounts[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L203-L232
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
203:        for (uint256 i; i < _blsPubKeys.length; ++i) {

232:        }
```

```diff
diff --git a/contracts/liquid-staking/StakingFundsVault.sol b/contracts/liquid-staking/StakingFundsVault.sol
index ecc185c..0d71332 100644
--- a/contracts/liquid-staking/StakingFundsVault.sol
+++ b/contracts/liquid-staking/StakingFundsVault.sol
@@ -200,7 +200,7 @@ contract StakingFundsVault is
         address _recipient,
         bytes[] calldata _blsPubKeys
     ) external nonReentrant {
-        for (uint256 i; i < _blsPubKeys.length; ++i) {
+        for (uint256 i; i < _blsPubKeys.length;) {
             require(
                 liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
                 "Unknown BLS public key"
@@ -229,6 +229,10 @@ contract StakingFundsVault is
             require(address(token) != address(0), "Invalid BLS key");
             require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
             _distributeETHRewardsToUserForToken(msg.sender, address(token), token.balanceOf(msg.sender), _recipient);
+
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L266-L268
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
266:        for (uint256 i; i < _blsPublicKeys.length; ++i) {

268:        }
```

```diff
diff --git a/contracts/liquid-staking/StakingFundsVault.sol b/contracts/liquid-staking/StakingFundsVault.sol
index ecc185c..f28b8a6 100644
--- a/contracts/liquid-staking/StakingFundsVault.sol
+++ b/contracts/liquid-staking/StakingFundsVault.sol
@@ -263,8 +263,11 @@ contract StakingFundsVault is

         updateAccumulatedETHPerLP();

-        for (uint256 i; i < _blsPublicKeys.length; ++i) {
+        for (uint256 i; i < _blsPublicKeys.length;) {
             require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
+            unchecked {
+                ++i;
+            }
         }

         syndicate.unstake(address(this), _sETHRecipient, _blsPublicKeys, _amounts);

```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L276-L279
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
276:        for (uint256 i; i < _blsPubKeys.length; ++i) {
277:            LPToken token = lpTokenForKnot[_blsPubKeys[i]];
278:            totalAccumulated += previewAccumulatedETH(_user, token);
279:        }
```

```diff
diff --git a/contracts/liquid-staking/StakingFundsVault.sol b/contracts/liquid-staking/StakingFundsVault.sol
index ecc185c..1ef7ff1 100644
--- a/contracts/liquid-staking/StakingFundsVault.sol
+++ b/contracts/liquid-staking/StakingFundsVault.sol
@@ -273,9 +273,12 @@ contract StakingFundsVault is
     /// @notice Preview total ETH accumulated by a staking funds LP token holder associated with many KNOTs that have minted derivatives
     function batchPreviewAccumulatedETHByBLSKeys(address _user, bytes[] calldata _blsPubKeys) external view returns (uint256) {
         uint256 totalAccumulated;
-        for (uint256 i; i < _blsPubKeys.length; ++i) {
+        for (uint256 i; i < _blsPubKeys.length;) {
             LPToken token = lpTokenForKnot[_blsPubKeys[i]];
             totalAccumulated += previewAccumulatedETH(_user, token);
+            unchecked {
+                ++i;
+            }
         }
         return totalAccumulated;
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L286-L288
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
286:        for (uint256 i; i < _token.length; ++i) {
287:            totalAccumulated += previewAccumulatedETH(_user, _token[i]);
288:        }
```

```diff
diff --git a/contracts/liquid-staking/StakingFundsVault.sol b/contracts/liquid-staking/StakingFundsVault.sol
index ecc185c..6f694c5 100644
--- a/contracts/liquid-staking/StakingFundsVault.sol
+++ b/contracts/liquid-staking/StakingFundsVault.sol
@@ -283,8 +283,11 @@ contract StakingFundsVault is
     /// @notice Preview total ETH accumulated by a staking funds LP token holder associated with many KNOTs that have minted derivatives
     function batchPreviewAccumulatedETH(address _user, LPToken[] calldata _token) external view returns (uint256) {
         uint256 totalAccumulated;
-        for (uint256 i; i < _token.length; ++i) {
+        for (uint256 i; i < _token.length;) {
             totalAccumulated += previewAccumulatedETH(_user, _token[i]);
+            unchecked {
+                ++i;
+            }
         }
         return totalAccumulated;
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L211-L237
```solidity
File: /contracts/syndicate/Syndicate.sol
211:        for (uint256 i; i < _blsPubKeys.length; ++i) {

237:        }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..478b2b1 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -208,7 +208,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
         // Make sure we have the latest accrued information
         updateAccruedETHPerShares();

-        for (uint256 i; i < _blsPubKeys.length; ++i) {
+        for (uint256 i; i < _blsPubKeys.length;) {
             bytes memory _blsPubKey = _blsPubKeys[i];
             uint256 _sETHAmount = _sETHAmounts[i];

@@ -234,6 +234,10 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
             if (!transferResult) revert UnableToStakeFreeFloatingSlot();

             emit Staked(_blsPubKey, _sETHAmount);
+
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L259-L279
```solidity
File: /contracts/syndicate/Syndicate.sol
259:        for (uint256 i; i < _blsPubKeys.length; ++i) {

279:        }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..0b13518 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -256,7 +256,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
         // Claim all ETH owed before unstaking but even if nothing is owed `updateAccruedETHPerShares` will be called
         _claimAsStaker(_unclaimedETHRecipient, _blsPubKeys);

-        for (uint256 i; i < _blsPubKeys.length; ++i) {
+        for (uint256 i; i < _blsPubKeys.length;) {
             bytes memory _blsPubKey = _blsPubKeys[i];
             uint256 _sETHAmount = _sETHAmounts[i];
             if (sETHStakedBalanceForKnot[_blsPubKey][msg.sender] < _sETHAmount) revert NothingStaked();
@@ -276,6 +276,10 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
             if (!transferResult) revert TransferFailed();

             emit UnStaked(_blsPubKey, _sETHAmount);
+
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L301-L332
```solidity
File: /contracts/syndicate/Syndicate.sol
301:        for (uint256 i; i < _blsPubKeys.length; ++i) {

332:        }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..da26ca4 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -298,7 +298,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
         // Make sure we have the latest accrued information for all shares
         updateAccruedETHPerShares();

-        for (uint256 i; i < _blsPubKeys.length; ++i) {
+        for (uint256 i; i < _blsPubKeys.length;) {
             bytes memory _blsPubKey = _blsPubKeys[i];
             if (!isKnotRegistered[_blsPubKey]) revert KnotIsNotRegisteredWithSyndicate();

@@ -329,6 +329,9 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
                     true
                 );
             }
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L346-L348
```solidity
File: /contracts/syndicate/Syndicate.sol
346:        for (uint256 i; i < _blsPubKeys.length; ++i) {
347:            _updateCollateralizedSlotOwnersLiabilitySnapshot(_blsPubKeys[i]);
348:        }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..da74f0b 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -343,8 +343,11 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
     function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {
         uint256 numOfKeys = _blsPubKeys.length;
         if (numOfKeys == 0) revert EmptyArray();
-        for (uint256 i; i < _blsPubKeys.length; ++i) {
+        for (uint256 i; i < _blsPubKeys.length;) {
             _updateCollateralizedSlotOwnersLiabilitySnapshot(_blsPubKeys[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L420-L434
```solidity
File: /contracts/syndicate/Syndicate.sol
420:        for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

434:            }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..6645f63 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -417,7 +417,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
         (address stakeHouse,,,,,) = getStakeHouseUniverse().stakeHouseKnotInfo(_blsPubKey);

         // Find the collateralized SLOT owner and work out how much they're owed
-        for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
+        for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot;) {
             address collateralizedOwnerAtIndex = getSlotRegistry().getCollateralisedOwnerAtIndex(_blsPubKey, i);
             if (collateralizedOwnerAtIndex == _staker) {
                 uint256 balance = getSlotRegistry().totalUserCollateralisedSLOTBalanceForKnot(
@@ -432,6 +432,9 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
                 }
                 break;
             }
+            unchecked {
+                ++i;
+            }
         }

         return currentAccrued - claimedPerCollateralizedSlotOwnerOfKnot[_blsPubKey][_staker];
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L513-L523
```solidity
File: /contracts/syndicate/Syndicate.sol
513:       for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

523:      }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..1f2cd69 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -510,7 +510,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
                     address collateralizedOwnerAtIndex = getSlotRegistry().getCollateralisedOwnerAtIndex(_blsPubKey, 0);
                     accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;
                 } else {
-                    for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
+                    for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot;) {
                         address collateralizedOwnerAtIndex = getSlotRegistry().getCollateralisedOwnerAtIndex(_blsPubKey, i);
                         uint256 balance = getSlotRegistry().totalUserCollateralisedSLOTBalanceForKnot(
                             stakeHouse,
@@ -520,6 +520,9 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S

                         accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
                             balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);
+                            unchecked {
+                                ++i;
+                            }
                     }
                 }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L585-L593
```solidity
File: /contracts/syndicate/Syndicate.sol
585:        for (uint256 i; i < _priorityStakers.length; ++i) {

593:        }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..653bc83 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -582,7 +582,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
     /// @dev Business logic for adding priority stakers to the syndicate
     function _addPriorityStakers(address[] memory _priorityStakers) internal {
         if (_priorityStakers.length == 0) revert EmptyArray();
-        for (uint256 i; i < _priorityStakers.length; ++i) {
+        for (uint256 i; i < _priorityStakers.length;) {
             address staker = _priorityStakers[i];

             if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();
@@ -590,6 +590,9 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
             isPriorityStaker[staker] = true;

             emit PriorityStakerRegistered(staker);
+            unchecked {
+                ++i;
+            }
         }
     }
```


https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L598-L606
```solidity
File: /contracts/syndicate/Syndicate.sol
598:        for (uint256 i; i < _blsPublicKeys.length; ++i) {

606:        }
```

```diff
diff --git a/contracts/syndicate/Syndicate.sol b/contracts/syndicate/Syndicate.sol
index a3c6450..e8bf33a 100644
--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -595,7 +595,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S

     /// @dev Business logic for de-registering a set of knots from the syndicate and doing the required snapshots to ensure historical earnings are preserved
     function _deRegisterKnots(bytes[] calldata _blsPublicKeys) internal {
-        for (uint256 i; i < _blsPublicKeys.length; ++i) {
+        for (uint256 i; i < _blsPublicKeys.length;) {
             bytes memory blsPublicKey = _blsPublicKeys[i];

             // Do one final snapshot of ETH owed to the collateralized SLOT owners so they can claim later
@@ -603,6 +603,9 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S

             // Execute the business logic for de-registering the single knot
             _deRegisterKnot(blsPublicKey);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L392-L397
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
392:        for(uint256 i; i < _blsPubKeys.length; ++i) {

397:        }
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..f2c7713 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -389,11 +389,14 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         address smartWallet = smartWalletOfNodeRunner[msg.sender];
         require(smartWallet != address(0), "Unknown node runner");

-        for(uint256 i; i < _blsPubKeys.length; ++i) {
+        for(uint256 i; i < _blsPubKeys.length;) {
             require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

             // check that the node runner doesn't claim rewards for KNOTs from other smart wallets
             require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");
+            unchecked {
+                ++i;
+            }
         }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L587-L626
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
587:        for (uint256 i; i < numOfKnotsToProcess; ++i) {

626:        }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L63-L69
```solidity
File: /contracts/liquid-staking/ETHPoolLPFactory.sol
63:        for (uint256 i; i < numOfRotations; ++i) {

69:        }
```

[see resource](https://github.com/ethereum/solidity/issues/10695)

### Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L357
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
357:        require(_new != address(0) && _current != _new, "New is zero or current");
```
### Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.
I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here:

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L291
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
291:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

328:        require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

332:      require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

393:            require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

436:        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
437:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

469:            require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

541:            require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

589:            require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

688:            require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L611-L612
```solidity
File: /contracts/syndicate/Syndicate.sol
611:        if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();
612:        if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L79
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
79:            require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

114:        require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

204:            require(
205:              liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
206:                "Unknown BLS public key"
207:            );
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L84
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
84:        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L149-L152
```solidity
File: /contracts/liquid-staking/GiantSavETHVaultPool.sol
149:    require(
150:         vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
151:                    "ETH is either staked or derivatives minted"
152:     );
```
### A modifier used only once and not being inherited should be inlined to save gas
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L49-L52
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
49:    modifier onlyManager {
50:        require(msg.sender == address(liquidStakingManager), "Not the savETH vault manager");
51:        _;
52:    }
```

### Use Shift Right/Left instead of Division/Multiplication
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

[relevant source](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L378
```solidity
File: /contracts/syndicate/Syndicate.sol
378:        return ethPerKnot / 2;
```

### Use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of byetes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L149-L152
```solidity
File: /contracts/liquid-staking/GiantSavETHVaultPool.sol
149:                require(
150:                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
151:                    "ETH is either staked or derivatives minted"
152:                );
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWallet.sol#L33-L36
```solidity
File:/contracts/smart-wallet/OwnableSmartWallet.sol
33:        require(
34:            initialOwner != address(0),
35:            "OwnableSmartWallet: Attempting to initialize with zero address owner"
36:        );


100:        require(
101:            isTransferApproved(owner(), msg.sender),
102:            "OwnableSmartWallet: Transfer is not allowed"
103:        ); // F: [OSW-4]


115:        require(
116:            to != address(0),
117:            "OwnableSmartWallet: Approval cannot be set for zero address"
118:        ); // F: [OSW-2A]
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L84
```solidity
File:/contracts/liquid-staking/SavETHVault.sol
84:        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

90:        require(msg.value == _amount, "Must provide correct amount of ETH");


```
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L79
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
79:      require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) ==false,"BLS public key is not part of LSD network");

114:        require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

120:        require(msg.value == _amount, "Must provide correct amount of ETH");

154:            require(address(token) != address(0), "No ETH staked for specified BLS key");

240:        require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

267:            require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L256-L258
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
256:        require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
257:        require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
258:        require(numberOfKnots == 0, "Cannot change ticker once house is created");

268:        require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");

280:        require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

291:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

328:        require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

331:        require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet "); 

332: require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

393:       require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

396:        require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

435:        require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");

437:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

455:            require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");

469:            require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

541:            require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

589:            require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

662:        require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
663:        require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

936:        require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

939:        require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
940:        require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

944:        require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L122
```solidity
File: /contracts/liquid-staking/ETHPoolLPFactory.sol
122:            require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

130:            require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

```
I suggest shortening the revert strings to fit in 32 bytes, or using custom errors.




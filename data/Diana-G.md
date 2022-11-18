## G-01 x += y COSTS MORE GAS THAN x = x + y FOR STATE VARIABLES (SAME APPLIES FOR x -= y , x = x - y)

_There are 22 instances of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol

```
File: contracts/liquid-staking/GiantPoolBase.sol

39: idleETH += msg.value;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

770: stakedKnotsOfSmartWallet[smartWallet] += 1;
782: numberOfKnots += 1;
839: numberOfKnots += 1;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol

```
File: contracts/liquid-staking/SavETHVault.sol

71: totalAmount += amount;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol

```
File: contracts/liquid-staking/StakingFundsVault.sol

58: totalShares += 4 ether;
97: totalAmount += amount;
278: totalAccumulated += previewAccumulatedETH(_user, token);
287: totalAccumulated += previewAccumulatedETH(_user, _token[i]);
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

65: totalClaimed += due;
85: accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

```
File: contracts/syndicate/Syndicate.sol

185: accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);
190: accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
225: totalFreeFloatingShares += _sETHAmount;
226: sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
227: sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
317: totalClaimed += unclaimedUserShare;
430: currentAccrued +=
511: accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;
521: accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
558: numberOfRegisteredKnots += knotsToRegister;
658: totalClaimed += unclaimedUserShare;
```

-----------

## G-02 INCREMENTS CAN BE UNCHECKED

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used

_There are 38 instances of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```
File: contracts/liquid-staking/ETHPoolLPFactory.sol

63: for (uint256 i; i < numOfRotations; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

```
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

38: for (uint256 i; i < numOfVaults; ++i)
64: for (uint256 i; i < numOfVaults; ++i) {
90: for (uint256 i; i < _stakingFundsVaults.length; ++i) {
117: for (uint256 i; i < numOfRotations; ++i) {
135: for (uint256 i; i < numOfVaults; ++i) {
183: for (uint256 i; i < _lpTokens.length; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol

```
File: contracts/liquid-staking/GiantPoolBase.sol

76: for (uint256 i; i < amountOfTokens; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

42: for (uint256 i; i < numOfSavETHVaults; ++i) {
78: for (uint256 i; i < numOfVaults; ++i) {
82: for (uint256 j; j < _lpTokens[i].length; ++j) {
128: for (uint256 i; i < numOfRotations; ++i) {
146: for (uint256 i; i < numOfVaults; ++i) {
148: for (uint256 j; j < _lpTokens[i].length; ++j) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

392: for(uint256 i; i < _blsPubKeys.length; ++i) {
465: for(uint256 i; i < len; ++i) {
538: for (uint256 i; i < numOfValidators; ++i) {
587: for (uint256 i; i < numOfKnotsToProcess; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol

```
File: contracts/liquid-staking/SavETHVault.sol

63: for (uint256 i; i < numOfValidators; ++i) {
103: for (uint256 i; i < numOfTokens; ++i) {
116: for (uint256 i; i < numOfTokens; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol

```
File: contracts/liquid-staking/StakingFundsVault.sol

78: for (uint256 i; i < numOfValidators; ++i) {
152: for (uint256 i; i < numOfTokens; ++i) {
166: for (uint256 i; i < numOfTokens; ++i) {
203: for (uint256 i; i < _blsPubKeys.length; ++i) {
266: for (uint256 i; i < _blsPublicKeys.length; ++i) {
276:  for (uint256 i; i < _blsPubKeys.length; ++i) {
286: for (uint256 i; i < _token.length; ++i) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

```
File: contracts/syndicate/Syndicate.sol

211: for (uint256 i; i < _blsPubKeys.length; ++i) {
259: for (uint256 i; i < _blsPubKeys.length; ++i) {
301: for (uint256 i; i < _blsPubKeys.length; ++i) {
346: for (uint256 i; i < _blsPubKeys.length; ++i) {
420: for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
513: for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
560: for (uint256 i; i < knotsToRegister; ++i) {
585: for (uint256 i; i < _priorityStakers.length; ++i) {
598: for (uint256 i; i < _blsPublicKeys.length; ++i) {
648: for (uint256 i; i < _blsPubKeys.length; ++i) {
```

--------------

## G-03 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

921: function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {
```

---------

## G-04 REQUIRE() OR REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

_There are 40 instances of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```
File: contracts/liquid-staking/ETHPoolLPFactory.sol

122: require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
130: require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol

```
File: contracts/liquid-staking/GiantPoolBase.sol

55: require(idleETH >= _amount, "Come back later or withdraw less ETH");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

149-151: require(
                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
                    "ETH is either staked or derivatives minted"
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

256: require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
257: require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
258: require(numberOfKnots == 0, "Cannot change ticker once house is created");
268: require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
280: require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
291: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
328: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
331: require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");
332: require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
393: require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
396: require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");
435: require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");
436: require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
437: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
455: require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
469: require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
541: require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
589: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
662: require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
663: require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
936: require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
939: require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault"
940: require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
944: require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol

```
File: contracts/liquid-staking/SavETHVault.sol

64: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
84: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
90: require(msg.value == _amount, "Must provide correct amount of ETH");
204: require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol

```
File: contracts/liquid-staking/StakingFundsVault.sol

79: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
114: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
120: require(msg.value == _amount, "Must provide correct amount of ETH")
154: require(address(token) != address(0), "No ETH staked for specified BLS key");
240: require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
267: require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol

```
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

33-35: require(
            initialOwner != address(0),
            "OwnableSmartWallet: Attempting to initialize with zero address owner"
100-102: require(
            isTransferApproved(owner(), msg.sender),
            "OwnableSmartWallet: Transfer is not allowed"
115-117: require(
            to != address(0),
            "OwnableSmartWallet: Approval cannot be set for zero address"
```

---------

## G-05 DUPLICATED REQUIRE() OR REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

_There are 6 instances of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol

```
File: contracts/liquid-staking/GiantLP.sol

30: require(msg.sender == pool, "Only pool");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

```
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

147: require(msg.sender == address(lpTokenETH), "Caller is not giant LP");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol

```
File: contracts/liquid-staking/SavETHVault.sol

101: require(numOfTokens > 0, "Empty arrays");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

72: require(numOfVaults > 0, "Empty arrays");
73: require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
74: require(numOfVaults == _amounts.length, "Inconsistent arrays");
```

-----------

## G-06 Don't compare boolean expressions to boolean literals

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value. I suggest using `if(!directValue)` instead of `if(directValue == false)`

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

_There are 18 instances of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

150: vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

291: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
328: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
332: require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
393: require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
436: require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
437: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
469: require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
541: require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
589: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
688: require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol

```
File: contracts/liquid-staking/SavETHVault.sol

64: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
84: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol

```
File: contracts/liquid-staking/StakingFundsVault.sol

79: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
114: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
205: liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

```
File: contracts/syndicate/Syndicate.sol

611: if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();
612: if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
```

------

## G-07 SPLITTING REQURE() STATEMENTS THAT USE && SAVES GAS

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol

```
File: contracts/liquid-staking/LiquidStakingManager.sol

357: require(_new != address(0) && _current != _new, "New is zero or current");
```

--------

## G-08 Multiplication or division by 2 should use bit shifting

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol

```
File: contracts/syndicate/Syndicate.sol

378: return ethPerKnot / 2;
```

-------


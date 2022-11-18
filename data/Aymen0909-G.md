# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Mappings related to the same data should be combined into a single mapping with the help of struct  | 9 |
| 2      | State variables only set in the constructor should be declared `immutable`  | 3 |
| 3      | Double write to storage in the `burnLPToken` function  |  1 |
| 4      | Redundant non-zero address check in `burnLPTokensForETHByBLS` function  |  1 |
| 5      | Use `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()`  |  2 |
| 6      | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  24 |
| 7      | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  36  |
| 8      | Don't compare boolean expressions to boolean literals  |  16 |
| 9      | Use `calldata` instead of `memory` for function parameters type  |  5 |
| 10      | Splitting  `require()` statements that uses && saves gas  |  1 |
| 11     | `memory` values should be emitted in events instead of `storage` ones  |  1 |


## Findings

### 1- Mappings related to the same data should be combined into a single mapping with the help of struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 9 instances of this issue :

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

These mappings could be refactored into the following struct and mapping for example :

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

## 2- State variables only set in the constructor should be declared `immutable` :

The state variables that rae only set in the constructor and there is no way to change their value later should be declared `immutable`, ths avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

There are 3 instances of this issue:

File: contracts/liquid-staking/GiantLP.sol [Line 11](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L11)
```
address public pool;
```

File: contracts/liquid-staking/GiantLP.sol [Line 14](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L14)
```
ITransferHookProcessor public transferHookProcessor;
```

File: contracts/liquid-staking/OptionalHouseGatekeeper.sol [Line 12](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L12)
```
ILiquidStakingManager public liquidStakingManager;
```

### 3- Double write to storage in the `burnLPToken` function :

In the `burnLPToken` function (in the SavETHVault contract) there is a doube write to storage for the `dETHDetails` variable as the function uses a `storage` pointer, the second save should be removed to save gas (~4k gas saved)

File: contracts/liquid-staking/SavETHVault.sol [Line 168-170](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L168-L170)
```solidity
// first save
dETHDetails.withdrawn = true;
dETHDetails.savETHBalance = savETHBalance;

// second save
dETHForKnot[blsPublicKeyOfKnot] = dETHDetails;
```

### 4- Redundant non-zero address check in `burnLPTokensForETHByBLS` function :

The  zero address check inside the `burnLPTokensForETHByBLS` function is redundant as the `burnLPForETH` function also contains the same check, so it must be removed to save gas.

This check must be removed : 

File: contracts/liquid-staking/StakingFundsVault.sol [Line 154](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L154)

```
require(address(token) != address(0), "No ETH staked for specified BLS key");
```

Because this check does the same verification later : 

File: contracts/liquid-staking/StakingFundsVault.sol [Line 177](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L177)

```
require(address(_lpToken) != address(0), "Zero address specified");
```


### 5- Use `unchecked {}` for subtractions where the operands cannot underflow because of a previous  `require()`:

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 2 instances of this issue:

File: contracts/liquid-staking/GiantPoolBase.sol [Line 57](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L57)
```
idleETH -= _amount;
```

The above operation cannot underflow due to the check [require(idleETH >= _amount, "Come back later or withdraw less ETH");](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L55) and should be modified as follows :

```
unchecked {idleETH -= _amount;}
```

File: contracts/syndicate/Syndicate.sol [Line 272-273](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L272-L273)
```
sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;
```

The above operations cannot underflow due to the check 

[if (sETHStakedBalanceForKnot[_blsPubKey][msg.sender] < _sETHAmount) revert NothingStaked();](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L262) 

and should be modified as follows :

```
unchecked {
    sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
    sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;
}
```

### 6- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 24 instances of this issue:

```
File: contracts/liquid-staking/GiantPoolBase.sol

39       idleETH += msg.value;
57       idleETH -= _amount;

File: contracts/liquid-staking/GiantMevAndFeesPool.sol

40       idleETH -= _ETHTransactionAmounts[i];

File: contracts/liquid-staking/GiantSavETHVaultPool.sol

46       idleETH -= transactionAmount;

File: contracts/liquid-staking/LiquidStakingManager.sol

615      stakedKnotsOfSmartWallet[smartWallet] -= 1;
770      stakedKnotsOfSmartWallet[smartWallet] += 1;
782      numberOfKnots += 1;
839      numberOfKnots += 1;

File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

65       totalClaimed += due;
85       accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;

File: contracts/syndicate/Syndicate.sol

185      accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);
190      accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
225      totalFreeFloatingShares += _sETHAmount;
226      sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
227      sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
269      totalFreeFloatingShares -= _sETHAmount;
272      sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
273      sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;
317      totalClaimed += unclaimedUserShare;
511      accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;
558      numberOfRegisteredKnots += knotsToRegister;
621      totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];
624      numberOfRegisteredKnots -= 1;
658      totalClaimed += unclaimedUserShare;
```

### 7- ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 36 instances of this issue:

```
File: contracts/liquid-staking/GiantPoolBase.sol

76       for (uint256 i; i < amountOfTokens; ++i)

File: contracts/liquid-staking/GiantMevAndFeesPool.sol

38       for (uint256 i; i < numOfVaults; ++i)
64       for (uint256 i; i < numOfVaults; ++i)
117      for (uint256 i; i < numOfRotations; ++i)
135      for (uint256 i; i < numOfVaults; ++i)
183      for (uint256 i; i < _lpTokens.length; ++i)

File: contracts/liquid-staking/GiantSavETHVaultPool.sol

42       for (uint256 i; i < numOfSavETHVaults; ++i)
78       for (uint256 i; i < numOfVaults; ++i)
82       for (uint256 j; j < _lpTokens[i].length; ++j)
128      for (uint256 i; i < numOfRotations; ++i)
146      for (uint256 i; i < numOfVaults; ++i)
148      for (uint256 j; j < _lpTokens[i].length; ++j)

File: contracts/liquid-staking/LiquidStakingManager.sol

392      for(uint256 i; i < _blsPubKeys.length; ++i)
465      for(uint256 i; i < len; ++i)
538      for (uint256 i; i < numOfValidators; ++i)
587      for (uint256 i; i < numOfKnotsToProcess; ++i)

File: contracts/liquid-staking/SavETHVault.sol

63       for (uint256 i; i < numOfValidators; ++i)
103      for (uint256 i; i < numOfTokens; ++i)
116      for (uint256 i; i < numOfTokens; ++i)

File: contracts/liquid-staking/StakingFundsVault.sol

78       for (uint256 i; i < numOfValidators; ++i)
152      for (uint256 i; i < numOfTokens; ++i)
166      for (uint256 i; i < numOfTokens; ++i)
203      for (uint256 i; i < _blsPubKeys.length; ++i)
266      for (uint256 i; i < _blsPublicKeys.length; ++i)
276      for (uint256 i; i < _blsPubKeys.length; ++i) 
286      for (uint256 i; i < _token.length; ++i)

File: contracts/syndicate/Syndicate.sol

211      for (uint256 i; i < _blsPubKeys.length; ++i)
259      for (uint256 i; i < _blsPubKeys.length; ++i)
301      for (uint256 i; i < _blsPubKeys.length; ++i)
346      for (uint256 i; i < _blsPubKeys.length; ++i)
420      for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i)
513      for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i)
560      for (uint256 i; i < knotsToRegister; ++i)
585      for (uint256 i; i < _priorityStakers.length; ++i)
598      for (uint256 i; i < _blsPublicKeys.length; ++i)
648      for (uint256 i; i < _blsPubKeys.length; ++i)
```

### 8- Don't compare boolean expressions to boolean literals :

The following formulas should be used : `if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

There are 16 instances of this issue:

```
File: contracts/liquid-staking/LiquidStakingManager.sol

291      require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
328      require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
332      require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
332      require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
393      require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
436      require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
437      require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
469      require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
541      require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
589      require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
688      require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

File: contracts/liquid-staking/SavETHVault.sol

64       require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
84       require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

File: contracts/liquid-staking/StakingFundsVault.sol

79       require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
114      require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
204      require(
                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
                "Unknown BLS public key"
            );
```

### 9- Use `calldata` instead of `memory` for function parameters type :

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

There are 5 instances of this issue :

```
File: contracts/syndicate/Syndicate.sol

337     function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey)
343     function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys)
359     function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user)
491     function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey)
555     function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots)
583     function _addPriorityStakers(address[] memory _priorityStakers)
610     function _deRegisterKnot(bytes memory _blsPublicKey)
```

### 10- Splitting `require()` statements that uses && saves gas :

There is 1 instances of this issue :

File: contracts/liquid-staking/LiquidStakingManager.sol [Line 357](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L357)
```
require(_new != address(0) && _current != _new, "New is zero or current");
```

### 11- `memory` values should be emitted in events instead of `storage` ones :

Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead, this will save **~101 GAS**

File: contracts/liquid-staking/LiquidStakingManager.sol [Line 270](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L270)
```
emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);
```
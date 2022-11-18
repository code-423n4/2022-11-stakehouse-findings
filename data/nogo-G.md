# Gas optimizations

## [G-00] Change `require()` to `revert` with custom errors will save gas

Custom Errors had been introduced since Solidity `0.8.4` and are more gas-efficient than `require()`. This code base is using extensively `require` which, if converted to `revert` might help reduced gas significantly.

```sol
contracts/liquid-staking/LSDNFactory.sol
  55: require(_liquidStakingManagerImplementation != address(0), "Zero Address");
```

## [G-01] Use `x = x + y` instead of `x += y`

`x = x + y` costs less than `x += y` (same for substraction).

```
// Syndicate.sol
accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);
accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
// GiantSavETHVaultPool.sol
idleETH -= transactionAmount;
```

## [G-02] Prefer `private` variables to avoid useless getters

Some public variables are not used any other contracts. Thus they can be declared as `private` instead of `public`. This will help reduce the deployment gas.

```
// LiquidStakingManager.sol
address public brand;
```

## [G-03] Use constant for known state variables

For `constant` variables, the value is fixed at compile-time and is introducing gas savings.

From the solidity doc:
> Compared to regular state variables, the gas costs of constant and immutable variables are much lower. For a constant variable, the expression assigned to it is copied to all the places where it is accessed and also re-evaluated each time. This allows for local optimizations. Immutable variables are evaluated once at construction time and their value is copied to all the places in the code where they are accessed. For these values, 32 bytes are reserved, even if they would fit in fewer bytes. Due to this, constant values can sometimes be cheaper than immutable values.

```
// LiquidStakingManager.sol
uint256 public MODULO = 100_00000;
```

## [G-04] Prefer comparing boolean expression to boolean literals

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

```
// LiquidStakingManager.sol
require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

```

## [G-05] `stakedKnotsOfSmartWallet[smartWallet]` not cached

`stakedKnotsOfSmartWallet[smartWallet]` will get data from storage. Caching this value in memory and reusing will allow gas consumption reduction.

Potential fix:
```
address knotOfSmartWallet = stakedKnotsOfSmartWallet[smartWallet];
require(knotOfSmartWallet == 0, "Not all KNOTs are minted");
require(knotOfSmartWallet != _newRepresentative, "Invalid rotation to same EOA");

// unauthorize old representative
_authorizeRepresentative(smartWallet, knotOfSmartWallet, false);
```

## [G-06] Using `calldata` instead of `memory` for read-only arguments in external functions saves gas

`memory` variables are allocated by the callee and their value can be modified inside the function (they're mutable).
`calldata` value is allocated by the caller, that's why it's gas cost is lower.

```
//
function initialize(
    address _contractOwner,
    uint256 _priorityStakingEndBlock,
    address[] memory _priorityStakers,
    bytes[] memory _blsPubKeysForSyndicateKnots
) external virtual override initializer {
```

## [G-07] Using `> 0` costs more gas than `!= 0` when used on a uint in a require() statement

```
// GiantSavETHVaultPool.sol
require(numOfSavETHVaults > 0, "Empty arrays");

// GiantMevAndFeesPool.sol
require(numOfRotations > 0, "Empty arrays");
require(numOfSavETHVaults > 0, "Empty arrays");

// LiquidStakingManager.sol
require(numOfKnotsToProcess > 0, "Empty array");

// ETHPoolLPFactory.sol
require(numOfRotations > 0, "Empty arrays");

// SavEthVault.sol
require(numOfTokens > 0, "Empty arrays");
```
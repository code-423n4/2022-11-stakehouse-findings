# Low Risk Issues

## [L-00] Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment,

```
// updateAccruedETHPerShares() - Syndicate.sol
        } else {
            // todo - check else case for any ETH lost
        }
```

## [L-01] All contracts should be using the same fixed compiler version

```sol
// LiquidStakingManager.sol
// LSDNFactory.sol
// GiantLP.sol
// ETHPoolLPFactory.sol
pragma solidity ^0.8.13;

// Syndicate.sol
pragma solidity 0.8.13;
```

## [L-02] All contracts should be using the latest solidity version

Currently contracts are using `0.8.13` (or `^0.8.13`) which has known bugs. See [solidity releases](https://github.com/ethereum/solidity/releases).

## [L-03] Opposite/Wrong require message can lead bad user experience

First instance:
The following check, as stated by the comment, is checking if the BLS public key is part of LSD network and is not banned.

```
// LiquidStakingManager.sol
require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
// should be: BLS public key is banned or already part of LSD network
```

Second instance:
Considering the definition of `isBLSPublicKeyBanned()` in `LiquidStakingManager`, this is checking whether the BLS public key is banned or not part of the LSD network.
```
// SavEthVault.sol
require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
// should be: BLS public key is banned or not part of LSD network
```

## [L-04] Confusing naming of a public function

As a public function, it can be used by external dev/smart contracts which could rely on the name to build a specific logic.

The following function is checking whether the `BLSPublicKey` is banned or part of the LSDNetwork.
```
function isBLSPublicKeyBanned(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) {
        return !isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot) || bannedBLSPublicKeys[_blsPublicKeyOfKnot] != address(0);
}
// Proposition: isBLSPublicKeyBannedOrNotPartOfLSDNetwork()
```

# Non-critical Issues

## [N‑00] NatSpec is incomplete

Multiple functions have an incomplete NetSpec. 

Example: `deployNewLiquidStakingDerivativeNetwork` in `LSDNFactory.sol` is missing the `_optionalCommission` param.

```
/// @notice Deploys a new LSDN and the liquid staking manger required to manage the network
/// @param _dao Address of the entity that will govern the liquid staking network
/// @param _stakehouseTicker Liquid staking derivative network ticker (between 3-5 chars)
function deployNewLiquidStakingDerivativeNetwork(
        address _dao,
        uint256 _optionalCommission,
        bool _deployOptionalHouseGatekeeper,
        string calldata _stakehouseTicker
) public returns (address) {
```
## [N-01] Increase smart contract readability by moving all events at the top

`CurrentStamp` is declared at *line 122* in `SavEthVault.sol`. Conisider moving the declaration line 23.


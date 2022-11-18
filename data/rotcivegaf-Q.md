# QA report

## Author: rotcivegaf

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Open TODO | 1 |

## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Typo | 19 |
| [N&#x2011;02] | Non-library/interface files should use fixed compiler versions, not floating ones | 19 |
| [N&#x2011;03] | No specified visibility | 2 |
| [N&#x2011;04] | Lint | 2 |
| [N&#x2011;05] | Event is missing `indexed` fields | 11 |
| [N&#x2011;06] | Unused events | 5 |

## Low Risk

### [L-01] Open TODO

Open TODO can point to architecture or programming issues that still need to be resolved.

```solidity
File: contracts/syndicate/Syndicate.sol

195            // todo - check else case for any ETH lost
```

## Non-critical

### [N‑01] Typo

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

/// @audit: Instane to Instance
 74    /// @param _newLPToken Instane of the new LP token (to be minted)

/// @audit: depoistor to depositor
124            // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied

/// @audit: depoistor to depositor
150            // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied
```

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit: publice to public
356    /// @notice Calculate the amount of unclaimed ETH for a given BLS publice key + free floating SLOT staker without factoring in unprocessed rewards

/// @audit: collatearlized to collateralized
398    /// @notice Preview the amount of unclaimed ETH available for a collatearlized SLOT staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract

/// @audit: numberOfCollateralisedSlotOwnersForKnot to numberOfCollateralizedSlotOwnersForKnot
416        uint256 numberOfCollateralisedSlotOwnersForKnot = getSlotRegistry().numberOfCollateralisedSlotOwnersForKnot(_blsPubKey);

420        for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

421              address collateralizedOwnerAtIndex = getSlotRegistry().getCollateralisedOwnerAtIndex(_blsPubKey, i);

423                uint256 balance = getSlotRegistry().totalUserCollateralisedSLOTBalanceForKnot(

/// @audit: amongs to amongst
490    /// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this amongs the current set of collateralized owners (a dynamic set of addresses and balances)

/// @audit: incomming to incoming
565            // incomming knot collateralized SLOT holders do not get historical earnings

/// @audit: funtion to function
654            // this means that user can call the funtion even if there is nothing to claim but the
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol

/// @audit: Inconsisent to Inconsistent
115        require(numOfTokens == _amounts.length, "Inconsisent array length");

/// @audit: determins to determines
227    /// @notice Utility function that determins whether an LP can be burned for dETH if the associated derivatives have been minted
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit: trigerringAddress to triggeringAddress
 51    event KnotStaked(bytes _blsPublicKeyOfKnot, address indexed trigerringAddress);

/// @audit: admiting to admitting
101    /// @notice address of optional gatekeeper for admiting new knots to the house created by the network

/// @audit: Unrecognised to Unrecognized
436        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

/// @audit: validtor to validator and initals to initials
476            // register validtor initals for each of the KNOTs

/// @audit: overriden to overridden
903    /// @dev Something that can be overriden during testing

/// @audit: overriden to overridden and customise to customize
920    /// @dev This can be overriden to customise fee percentages
```

### [N-02] Non-library/interface files should use fixed compiler versions, not floating ones

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

3 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

3 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol

3 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

3 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

3 pragma solidity ^0.8.13;
```

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

3 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/syndicate/SyndicateFactory.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/LPToken.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/GiantLP.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

3 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

3 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol

1 pragma solidity ^0.8.13;
```

```solidity
File: contracts/liquid-staking/OptionalGatekeeperFactory.sol

3 pragma solidity ^0.8.13;
```

### [N-03] No specified visibility

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

13    address implementation;
```

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

13    address implementation;
```

### [N‑04] Lint

Wrong indentation: Use always 4 spaces

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

72    external
73    override
74    payable
75    onlyOwner
76    returns (bytes memory)
```

Add space:

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

/// @audit: `==IDataStructures` to `== IDataStructures`
97            getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
```

### [N‑05] Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

16    event ETHWithdrawnByDepositor(address depositor, uint256 amount);

19    event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

22    event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

25    event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```

```solidity
File: contracts/liquid-staking/SavETHVault.sol

 19    event DETHRedeemed(address depositor, uint256 amount);

 22    event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

121    event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

25    event ETHDeposited(address sender, uint256 amount);

28    event ETHWithdrawn(address receiver, address admin, uint256 amount);

31    event ERC20Recovered(address admin, address recipient, uint256 amount);

34    event WETHUnwrapped(address admin, uint256 amount);
```

### [N‑06] Unused events

```solidity
File: contracts/liquid-staking/SavETHVault.sol

121    event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

25    event ETHDeposited(address sender, uint256 amount);

31    event ERC20Recovered(address admin, address recipient, uint256 amount);

34    event WETHUnwrapped(address admin, uint256 amount);
```

```solidity
File: contracts/syndicate/Syndicate.sol

45    event CollateralizedSLOTReCalibrated(bytes BLSPubKey);
```

# [01] Unsafe downcast

## Proof of Concept

Downcast operation in `SavETHVault.burnLPToken`.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L177

## Impact

Downcasting in solidity can lead to overflow. For example, if the result of `(dETHDetails.savETHBalance * _amount) / 24 ether` generate a value bigger than uint128, e.g. by a user input mistake and if the check on [L128](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L128) passes, the resulting `redemptionValue` will overflow, causing a silent failure/bad account/loss of funds when calling `getSavETHRegistry().withdraw()`.

## Recommended Mitigation Steps

Use a safe cast library for downcasting operations. E.g. [openzeppelin safe cast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast).

# [02] Missing storage gap for upgradeable contracts

Consider adding a `__gap[]` storage variable to allow for new storage variables in later versions.

See this [link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable issue. Please, refer to this [example](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol) for an implementation reference.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L11

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L33

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L16

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L20

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L36

# [03] Missing address(0) checks

If a variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L25

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L33

# [04] Floating the pragma version

The following contracts are not using fixed compiler versions.

contracts/liquid-staking/OptionalGatekeeperFactory.sol,
contracts/liquid-staking/OptionalHouseGatekeeper.sol,
contracts/liquid-staking/SavETHVaultDeployer.sol,
contracts/liquid-staking/StakingFundsVaultDeployer.sol,
contracts/smart-wallet/OwnableSmartWalletFactory.sol,
contracts/liquid-staking/LPTokenFactory.sol,
contracts/liquid-staking/GiantLP.sol,
contracts/liquid-staking/LPToken.sol,
contracts/liquid-staking/GiantPoolBase.sol,
contracts/liquid-staking/LSDNFactory.sol,
contracts/liquid-staking/GiantSavETHVaultPool.sol,
contracts/smart-wallet/OwnableSmartWallet.sol,
contracts/liquid-staking/SavETHVault.sol,
contracts/liquid-staking/GiantMevAndFeesPool.sol,
contracts/liquid-staking/StakingFundsVault.sol,
contracts/liquid-staking/LiquidStakingManager.sol,
contracts/liquid-staking/SyndicateRewardsProcessor.sol,
contracts/liquid-staking/ETHPoolLPFactory.sol,

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

# [05] Outdated version of `@openzeppelin/contracts-upgradeable`

The project is using the fixed version 4.5.0 for contracts-upgradeable. Consider using the latest version (4.8.0) to ensure the library contracts contains the latest security fixes.

# [06] Fix typo

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L476

# [07] Returning manual values and declaring returned named variables on the same function

Consider using only one approach for the same function.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L921

# [08] Missing natspec

Consider documenting all function to improve documentation.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L911

# [09] Order of functions 

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instances bellow shows public above external.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L514

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L524-L530

# [10] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L19

# [11] Draft openzeppelin dependencies

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L6

OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

## Recommendation

Ensure the development team is aware of the risks of using a draft contract or consider waiting until the contract is finalised.

Otherwise, make sure that development team are aware of the risks of using a draft OpenZeppelin contract and accept the risk-benefit trade-off.

# [12] Add a limit for the maximum number of characters per line

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider adding a limit of 120 characters or less to prevent large lines.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L46

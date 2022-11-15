# LSD Network - Stakehouse Contest QA Report

## Summary

The following gas optimization issues were found during the code audit:

1. Unsafe ERC20 Operation(s) (6 instances)
2. Unspecific Compiler Version Pragma (18 instances)
3. `block.number` may exceed `type(uint32).max` (2 instances)

Total 26 instances of 3 issues.

## 1. Unsafe ERC20 Operation(s) (6 instances)

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

```solidity
contracts/liquid-staking/GiantPoolBase.sol::86 => token.transfer(msg.sender, amount);

contracts/liquid-staking/GiantSavETHVaultPool.sol::108 => getDETH().transfer(msg.sender, dETHReceivedFromAllSavETHVaults);

contracts/liquid-staking/GiantMevAndFeesPool.sol::184 => _lpTokens[i].transfer(address(this), _lpTokens[i].balanceOf(address(this)));

contracts/syndicate/Syndicate.sol::233 => bool transferResult = sETH.transferFrom(msg.sender, address(this), _sETHAmount);

contracts/syndicate/Syndicate.sol::275 => bool transferResult = sETH.transfer(_sETHRecipient, _sETHAmount);

contracts/liquid-staking/LiquidStakingManager.sol::870 => sETH.approve(syndicate, (2 ** 256) - 1);
```

## 2. Unspecific Compiler Version Pragma (18 instances)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
contracts/liquid-staking/OptionalGatekeeperFactory.sol::3 => pragma solidity ^0.8.13;

contracts/liquid-staking/OptionalHouseGatekeeper.sol::1 => pragma solidity ^0.8.13;

contracts/liquid-staking/SavETHVaultDeployer.sol::1 => pragma solidity ^0.8.13;

contracts/liquid-staking/StakingFundsVaultDeployer.sol::3 => pragma solidity ^0.8.13;

contracts/smart-wallet/OwnableSmartWalletFactory.sol::3 => pragma solidity ^0.8.13;

contracts/liquid-staking/LPTokenFactory.sol::1 => pragma solidity ^0.8.13;

contracts/liquid-staking/GiantLP.sol::1 => pragma solidity ^0.8.13;

contracts/liquid-staking/LPToken.sol::1 => pragma solidity ^0.8.13;

contracts/liquid-staking/GiantPoolBase.sol::1 => pragma solidity ^0.8.13;

contracts/liquid-staking/LSDNFactory.sol::1 => pragma solidity ^0.8.13;

contracts/liquid-staking/GiantSavETHVaultPool.sol::1 => pragma solidity ^0.8.13;

contracts/smart-wallet/OwnableSmartWallet.sol::3 => pragma solidity ^0.8.13;

contracts/liquid-staking/SavETHVault.sol::3 => pragma solidity ^0.8.13;

contracts/liquid-staking/GiantMevAndFeesPool.sol::1 => pragma solidity ^0.8.13;

contracts/liquid-staking/StakingFundsVault.sol::3 => pragma solidity ^0.8.13;

contracts/liquid-staking/LiquidStakingManager.sol::3 => pragma solidity ^0.8.13;

contracts/liquid-staking/SyndicateRewardsProcessor.sol::3 => pragma solidity ^0.8.13;

contracts/liquid-staking/ETHPoolLPFactory.sol::3 => pragma solidity ^0.8.13;
```

## 3. `block.number` may exceed `type(uint32).max` (2 instances)

While this currently equates to around 1260 years, if there's a hard fork which makes block times much more frequent (e.g. to compete with Solana), then this limit may be reached much faster than expected, and transfers and delegations will remain stuck at their existing settings.

```solidity
contracts/syndicate/Syndicate.sol::218 => if (block.number < priorityStakingEndBlock && !isPriorityStaker[_onBehalfOf]) revert NotPriorityStaker();

contracts/syndicate/Syndicate.sol::482 => if (_priorityStakingEndBlock > block.number) {
```

### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

To circumvent ERC20's approve functions race-condition vulnerability use OpenZeppelin's SafeERC20 library's safe{Increase|Decrease}Allowance functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

Example
🤦 Bad:

IERC20(token).transferFrom(msg.sender, address(this), amount);
🚀 Good (using OpenZeppelin's SafeERC20):

import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";

// ...

IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
🚀 Good (using require):

bool success = IERC20(token).transferFrom(msg.sender, address(this), amount);
require(success, "ERC20 transfer failed");

#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::184 => _lpTokens[i].transfer(address(this), _lpTokens[i].balanceOf(address(this)));
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::86 => token.transfer(msg.sender, amount);
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::108 => getDETH().transfer(msg.sender, dETHReceivedFromAllSavETHVaults);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::870 => sETH.approve(syndicate, (2 ** 256) - 1);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::233 => bool transferResult = sETH.transferFrom(msg.sender, address(this), _sETHAmount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::275 => bool transferResult = sETH.transfer(_sETHRecipient, _sETHAmount);
```
#### Tools used
Manual



=======================================================================================



### Unspecific Compiler Version Pragma

#### Impact
Issue Information: Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/OptionalGatekeeperFactory.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/OptionalHouseGatekeeper.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/SavETHVaultDeployer.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVaultDeployer.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::3 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::3 => pragma solidity ^0.8.13;
```
#### Tools used
Manual
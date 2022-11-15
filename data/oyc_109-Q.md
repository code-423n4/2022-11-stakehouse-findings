

## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

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
2022-11-stakehouse/contracts/smart-wallet/interfaces/IOwnableSmartWallet.sol::2 => pragma solidity ^0.8.10;
2022-11-stakehouse/contracts/smart-wallet/interfaces/IOwnableSmartWalletFactory.sol::2 => pragma solidity ^0.8.10;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::82 => require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::44 => lastInteractedTimestamp[_from] = block.timestamp;
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::45 => lastInteractedTimestamp[_to] = block.timestamp;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::96 => require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::67 => lastInteractedTimestamp[_from] = block.timestamp;
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::68 => lastInteractedTimestamp[_to] = block.timestamp;
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::141 => bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::184 => require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::230 => require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
```

## [L-03] Unused receive()/fallback() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::629 => receive() external payable {}
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::98 => receive() external payable {}
```

## [L-04] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

approve is subject to a known front-running attack. Consider using safeApprove() or safeIncreaseAllowance() or safeDecreaseAllowance() instead

```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::870 => sETH.approve(syndicate, (2 ** 256) - 1);
```

## [L-05] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::184 => _lpTokens[i].transfer(address(this), _lpTokens[i].balanceOf(address(this)));
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::86 => token.transfer(msg.sender, amount);
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::108 => getDETH().transfer(msg.sender, dETHReceivedFromAllSavETHVaults);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::233 => bool transferResult = sETH.transferFrom(msg.sender, address(this), _sETHAmount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::275 => bool transferResult = sETH.transfer(_sETHRecipient, _sETHAmount);
```

## [L-08] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::31 => _mint(_recipient, _amount);
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::47 => _mint(_recipient, _amount);
```

## [L-09] Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

__gap is empty reserved space in storage that is recommended to be put in place in upgradeable contracts. It allows new state variables to be added in the future without compromising the storage compatibility with existing deployments

```
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

## [N-1] Use a more recent version of solidity

Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
2022-11-stakehouse/contracts/smart-wallet/interfaces/IOwnableSmartWallet.sol::2 => pragma solidity ^0.8.10;
2022-11-stakehouse/contracts/smart-wallet/interfaces/IOwnableSmartWalletFactory.sol::2 => pragma solidity ^0.8.10;
```

## [N-3] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::16 => event ETHWithdrawnByDepositor(address depositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::19 => event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::22 => event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::25 => event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::57 => event StakehouseJoined(bytes blsPubKey);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::69 => event NetworkTickerUpdated(string newTicker);
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::84 => event DAOCommissionUpdated(uint256 old, uint256 newCommission);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::19 => event DETHRedeemed(address depositor, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::22 => event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::121 => event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::25 => event ETHDeposited(address sender, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::28 => event ETHWithdrawn(address receiver, address admin, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::31 => event ERC20Recovered(address admin, address recipient, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::34 => event WETHUnwrapped(address admin, uint256 amount);
2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol::9 => event ETHReceived(uint256 amount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::39 => event ContractDeployed();
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::42 => event UpdateAccruedETH(uint256 unprocessed);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::45 => event CollateralizedSLOTReCalibrated(bytes BLSPubKey);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::48 => event KNOTRegistered(bytes BLSPubKey);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::51 => event KnotDeRegistered(bytes BLSPubKey);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::57 => event Staked(bytes BLSPubKey, uint256 amount);
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::60 => event UnStaked(bytes BLSPubKey, uint256 amount);
```

## [N-4] Missing SPDX license identifier

File should include SPDX-License-Identifier

```
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/OptionalHouseGatekeeper.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/SavETHVaultDeployer.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::1 => pragma solidity 0.8.13;
2022-11-stakehouse/contracts/syndicate/SyndicateErrors.sol::1 => pragma solidity 0.8.13;
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::1 => pragma solidity 0.8.13;
```

## [N-5] Missing NatSpec

Code should include NatSpec

```
2022-11-stakehouse/contracts/liquid-staking/OptionalGatekeeperFactory.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/liquid-staking/SavETHVaultDeployer.sol::1 => pragma solidity ^0.8.13;
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVaultDeployer.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/smart-wallet/interfaces/IOwnableSmartWalletFactory.sol::1 => // SPDX-License-Identifier: MIT
2022-11-stakehouse/contracts/syndicate/SyndicateErrors.sol::1 => pragma solidity 0.8.13;
```

## [N-7] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public.

```
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::176 => function totalRewardsReceived() public view override returns (uint256) {
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::34 => function depositETH(uint256 _amount) public payable {
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::514 => function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) {
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::239 => function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::458 => function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {
```

## [N-8] Adding a return statement when the function defines a named return variable, is redundant

It is not necessary to have both a named return and a return statement.

```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::921 => function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {
```

## [N-10] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::222 => /// @notice In preparation of a rage quit, restore sETH to a smart wallet which are recoverable with the execution methods in the event this step does not go to plan
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::116 => /// @notice Whether a BLS public key, that has been previously registered, is no longer part of the syndicate and its shares (free floating or SLOT) cannot earn any more rewards
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::119 => /// @notice Once a BLS public key is no longer part of the syndicate, the accumulated ETH per free floating SLOT share is snapshotted so historical earnings can be drawn down correctly
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::398 => /// @notice Preview the amount of unclaimed ETH available for a collatearlized SLOT staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::490 => /// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this amongs the current set of collateralized owners (a dynamic set of addresses and balances)
```

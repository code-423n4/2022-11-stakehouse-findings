# QA ISSUES FOR [2022-11-STAKEHOUSE](https://github.com/code-423n4/2022-11-stakehouse)

## [L-01] Not emiting events in some important functions

Description: Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

./contracts/liquid-staking/LPToken.sol

```
L32:   function init(
```

./contracts/syndicate/Syndicate.sol

```
L168:  function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
```

## [L-02] Empty receive and/or fallback function

./contracts/liquid-staking/LiquidStakingManager.sol

```
L629:  receive() external payable {}
```

./contracts/liquid-staking/SyndicateRewardsProcessor.sol

```
L98:   receive() external payable {}
```

## [N-01] Non-library/interface files should use fixed compiler versions, not floating ones

./contracts/liquid-staking/LSDNFactory.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/smart-wallet/OwnableSmartWalletFactory.sol

```
L3:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/GiantLP.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/StakingFundsVault.sol

```
L3:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/LPToken.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/smart-wallet/OwnableSmartWallet.sol

```
L3:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/StakingFundsVaultDeployer.sol

```
L3:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/GiantPoolBase.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/OptionalHouseGatekeeper.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/GiantSavETHVaultPool.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/OptionalGatekeeperFactory.sol

```
L3:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/SavETHVaultDeployer.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/LiquidStakingManager.sol

```
L3:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/ETHPoolLPFactory.sol

```
L3:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/SavETHVault.sol

```
L3:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/GiantMevAndFeesPool.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/LPTokenFactory.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/SyndicateRewardsProcessor.sol

```
L3:    pragma solidity ^0.8.13;
```

## [N-02] Not using the NatSpec format

./contracts/liquid-staking/SavETHVaultDeployer.sol

```
L1:    pragma solidity ^0.8.13;
```

./contracts/liquid-staking/OptionalGatekeeperFactory.sol

```
L1:    // SPDX-License-Identifier: MIT
```

./contracts/liquid-staking/StakingFundsVaultDeployer.sol

```
L1:    // SPDX-License-Identifier: MIT
```

## [N-03] Events should have 3 `indexed` fields where possible

./contracts/liquid-staking/LiquidStakingManager.sol

```
L66:   event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);
```

./contracts/liquid-staking/GiantSavETHVaultPool.sol

```
L16:   event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);
```

./contracts/liquid-staking/StakingFundsVault.sol

```
L28:   event ETHWithdrawn(address receiver, address admin, uint256 amount);

L31:   event ERC20Recovered(address admin, address recipient, uint256 amount);
```

./contracts/syndicate/Syndicate.sol

```
L63:   event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);
```

./contracts/liquid-staking/SavETHVault.sol

```
L22:   event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

L121:  event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

./contracts/liquid-staking/ETHPoolLPFactory.sol

```
L19:   event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

L22:   event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

L25:   event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```

./contracts/liquid-staking/SyndicateRewardsProcessor.sol

```
L12:   event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);
```

./contracts/liquid-staking/GiantPoolBase.sol

```
L19:   event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);
```

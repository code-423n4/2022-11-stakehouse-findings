# 2022-11-STAKEHOUSE

## Low Risk And Non-Critical Issues

### Events not emmited on important state changes

Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

_There is **1** instance of this issue:_

```solidity
File: contracts/syndicate/Syndicate.sol

168:  function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

### `public` functions not called by the contract should be declared `external` instead

_There are **9** instances of this issue:_

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

176:  function totalRewardsReceived() public view override returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

34:   function depositETH(uint256 _amount) public payable {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

29:   function batchDepositETHForStaking(
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

73:   function deployNewLiquidStakingDerivativeNetwork(
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

514:  function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/SavETHVault.sol

200:  function withdrawETHForStaking(
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

239:  function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/syndicate/Syndicate.sol

458:  function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

```solidity
File: contracts/syndicate/SyndicateFactory.sol

21:   function deploySyndicate(
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol

### Empty `receive()`/`fallback()` functions

_There are **2** instances of this issue:_

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

629:  receive() external payable {}
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

98:   receive() external payable {}
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

### Non-library/interface files should use fixed compiler versions, not floating ones

_There are **18** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

3:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/GiantLP.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```solidity
File: contracts/liquid-staking/LPToken.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPTokenFactory.sol

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

3:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/OptionalGatekeeperFactory.sol

3:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol

```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol

```solidity
File: contracts/liquid-staking/SavETHVault.sol

3:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVaultDeployer.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

3:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

3:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

3:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

3:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

3:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol

### Natspec is missing

_There are **4** instances of this issue:_

```solidity
File: contracts/liquid-staking/OptionalGatekeeperFactory.sol

1:    // SPDX-License-Identifier: MIT
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

1:    pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVaultDeployer.sol

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

1:    // SPDX-License-Identifier: MIT
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol

```solidity
File: contracts/syndicate/SyndicateErrors.sol

1:    pragma solidity 0.8.13;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateErrors.sol

### Event is missing `indexed` fields

_There are **12** instances of this issue:_

```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

19:   event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

22:   event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

25:   event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

19:   event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

16:   event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

66:   event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol

```solidity
File: contracts/liquid-staking/SavETHVault.sol

22:   event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

121:  event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

28:   event ETHWithdrawn(address receiver, address admin, uint256 amount);

31:   event ERC20Recovered(address admin, address recipient, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

12:   event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol

```solidity
File: contracts/syndicate/Syndicate.sol

63:   event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol

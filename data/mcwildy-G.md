# Qa Report

## STAKEHOUSE

### Use `external` visibility modifier for function that are not being invoked by the contract

[GiantSavETHVaultPool.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol):

```solidity
line#L29:  function batchDepositETHForStaking(
```

[LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol):

```solidity
line#L514: function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) {
```

[StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol):

```solidity
line#L239: function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
```

[SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol):

```solidity
line#L200: function withdrawETHForStaking(
```

[GiantMevAndFeesPool.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol):

```solidity
line#L176: function totalRewardsReceived() public view override returns (uint256) {
```

[Syndicate.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol):

```solidity
line#L458: function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {
```

[SyndicateFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol):

```solidity
line#L21:  function deploySyndicate(
```

[LSDNFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol):

```solidity
line#L73:  function deployNewLiquidStakingDerivativeNetwork(
```

[GiantPoolBase.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol):

```solidity
line#L34:  function depositETH(uint256 _amount) public payable {
```

### Missing natspec comments

https://docs.soliditylang.org/en/develop/natspec-format.html

[OptionalGatekeeperFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol):

```solidity
line#L1:   // SPDX-License-Identifier: MIT
```

[SavETHVaultDeployer.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVaultDeployer.sol):

```solidity
line#L1:   pragma solidity ^0.8.13;
```

[StakingFundsVaultDeployer.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol):

```solidity
line#L1:   // SPDX-License-Identifier: MIT
```

### Events should use all three `indexed` keywords for their parameters

[ETHPoolLPFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol):

```solidity
line#L19:  event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

line#L22:  event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

line#L25:  event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```

[GiantSavETHVaultPool.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol):

```solidity
line#L16:  event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);
```

[LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol):

```solidity
line#L66:  event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);
```

[GiantPoolBase.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol):

```solidity
line#L19:  event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);
```

[Syndicate.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol):

```solidity
line#L63:  event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);
```

[SyndicateRewardsProcessor.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol):

```solidity
line#L12:  event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);
```

[SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol):

```solidity
line#L22:  event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

line#L121: event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

[StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol):

```solidity
line#L28:  event ETHWithdrawn(address receiver, address admin, uint256 amount);

line#L31:  event ERC20Recovered(address admin, address recipient, uint256 amount);
```

### Events should be emmitted on every critical state changes

Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

[Syndicate.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol):

```solidity
line#L168: function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
```

[LiquidStakingManager.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol):

```solidity
line#L645: function _init(

line#L904: function _initSavETHVault(address _savETHVaultDeployer, address _lpTokenFactory) internal virtual {

line#L911: function _initStakingFundsVault(address _stakingFundsVaultDeployer, address _tokenFactory) internal virtual {
```

[SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol):

```solidity
line#L235: function _init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) internal {
```

[StakingFundsVault.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol):

```solidity
line#L371: function _init(LiquidStakingManager _liquidStakingNetworkManager, LPTokenFactory _lpTokenFactory) internal virtual {
```

[LPToken.sol](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol):

```solidity
line#L32:  function init(
```

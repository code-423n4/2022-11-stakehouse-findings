### NC-01 Incomplete NatSpec docs in the codebase

1. [GiantMevAndFeesPool::`previewAccumulatedETH`](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L82)
2. [LPTokenFactory::deployLPToken](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPTokenFactory.sol#L27)
3. [LPToken - all external methods](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L11)
4. [GiantPoolBase::depositETH](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L34)
5. [LSDNFactory::deployNewLiquidStakingDerivativeNetwork](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LSDNFactory.sol#L73)
6. [StakingFundsVault::claimRewards](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L199)
7. [Syndicate::claimAsCollateralizedSLOTOwner](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L290)

### NC-02 Use latest Solidity version

Codebase is currently using Solidity version 0.8.13 - it is a best practice to use latest Solidity version as you can get compiler optimisations and bugfixes.

### NC-03 Do not use a floating pragma, use a concrete version instead

The codebase is using floating pragma (for example ***`pragma*** solidity ^0.8.13;`) - Always use a concrete version so you get the same bytecode on each compilation for certain.

### NC-04 License comment should be on the first line in a smart contract file, not after the pragma statement

1. GiantMevAndFeesPool
2. OptionalHouseGatekeeper
3. SavETHVaultDeployer
4. LPTokenFactory
5. GiantLP
6. LPToken
7. SyndicateFactory
8. GiantPoolBase
9. LSDNFactory
10. SyndicateErrors
11. GiantSavETHVaultPool
12. Syndicate

### NC-05 Codebase is not using latest OpenZeppelin library version but one with vulnerabilities

[Here](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories) you can see all security advisories for the OpenZeppelin library and you can see that the version used in the codebase 

```
"@openzeppelin/contracts": "^4.5.0",
    "@openzeppelin/contracts-upgradeable": "4.5.0",
```

is not entirely safe. The contracts that are vulnerable are not used, but this is still a best practice for security - use latest version that has all security patches.

### NC-06 Code casts `address` to `address payable` even though it is not needed

1. [StakingFundsVaultDeployer](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L21)
2. [StakingFundsVault](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L47) 
3. [StakingFundsVault](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L215) 
4. [StakingFundsVault](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L364) 
5. [LiquidStakingManager](https://github.com/code-423n4/2022-11-stakehouse/blob/5f853d055d7aa1bebe9e24fd0e863ef58c004339/contracts/liquid-staking/LiquidStakingManager.sol#L219)
6. [LiquidStakingManager](https://github.com/code-423n4/2022-11-stakehouse/blob/5f853d055d7aa1bebe9e24fd0e863ef58c004339/contracts/liquid-staking/LiquidStakingManager.sol#L807)
7. [LiquidStakingManager](https://github.com/code-423n4/2022-11-stakehouse/blob/5f853d055d7aa1bebe9e24fd0e863ef58c004339/contracts/liquid-staking/LiquidStakingManager.sol#L900)
8. [LiquidStakingManager](https://github.com/code-423n4/2022-11-stakehouse/blob/5f853d055d7aa1bebe9e24fd0e863ef58c004339/contracts/liquid-staking/LiquidStakingManager.sol#L913)

### NC-07 Wrong error messages in require statements

[OwnableSmartWalletFactory](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L37) `require(owner !**=** address(*0*), 'Wallet cannot be address 0');` - should be `Owner cannot be address 0`

[LPToken](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L23) `require(**msg.sender** **==** deployer, "Only savETH vault");` should be `Only Deployer`

[SavETHVault](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L205) `require(address(**this**).balance **>=** _amount, "Insufficient withdrawal amount");` should be `Insufficient balance in vault to withdraw`

### NC-08 Typos in the code

`Allows a LP token owner to burn their tokens` → `Allows an LP token owner to burn their tokens`

`depoistor` → `depositor`

### NC-09 No need to do `== false` or `== true` on boolean expressions

There are 20+ occurrences of `== false` or `== true` in the code - just use `!boolexpr` for `== false` and `boolexpr` for `== true`

### NC-10 Open TODO in the code

- [https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L195](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L195)

Remove the TODO comment or resolve it

### NC-11 Use `type(uint256).max` instead of `2 ** 256 - 1`

- [https://github.com/code-423n4/2022-11-stakehouse/blob/5f853d055d7aa1bebe9e24fd0e863ef58c004339/contracts/liquid-staking/LiquidStakingManager.sol#L870](https://github.com/code-423n4/2022-11-stakehouse/blob/5f853d055d7aa1bebe9e24fd0e863ef58c004339/contracts/liquid-staking/LiquidStakingManager.sol#L870)

Use the built-in `type(uint256).max`

### NC-12 Not used event (dead code)

- [https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L121](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L121)

The event `CurrentStamp` is not used and is also in the middle of the smart contract. Remove it

### NC-13 Wrong parameter name in function `burn()`

Both [GiantLP::burn](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L34) and [LPToken::burn](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L51) have a `_recipient` named parameter. There is no `recipient` in a “burn” so rename parameter to “account” or “burnFrom”.

### L-01 Missing non-zero address checks in constructor or init method

1. [GiantMevAndFeesPool](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L17)
2. [OptionalHouseGatekeeper](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L14)
3. [GiantLP](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L19)
4. [LPToken](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L32)
5. [GiantSavETHVaultPool](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L18)

### L-02 Implementation contracts are not initialised in constructors

This should not be an issue since those contracts are only used to be cloned, but it is still a good practice to initialise them yourself

1. [StakingFundsVaultDeployer](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L15)
2. [SavETHVaultDeployer](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVaultDeployer.sol#L15)
3. [OwnableSmartWalletFactory](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L23)

### L-03 Functions that send ether with a low level `call` or do `ERC20::transfer` cals are missing `nonReentrant` modifier

1. [GiantPoolBase::withdrawLPTokens](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L69)
2. [StakingFundsVault::batchDepositETHForStaking](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L69)
3. [StakingFundsVault::depositETHForStaking](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L113)
4. [LiquidStakingManager::withdrawETHForKnot](https://github.com/code-423n4/2022-11-stakehouse/blob/5f853d055d7aa1bebe9e24fd0e863ef58c004339/contracts/liquid-staking/LiquidStakingManager.sol#L326)

### L-04 Use two-step ownership transfer pattern

`OwnableSmartWallet` inherits from OZ’s Ownable and also `LiquidStakingManager` has the `updateDAOAddress` functionality. Both are doing them in a one-step way, consider using a two-step ownership transfer pattern.
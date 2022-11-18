# Issues

### should add space between `///` and `@notice`

```
contracts/liquid-staking/LiquidStakingManager.sol

59: ///@notice signalize removal of representative from smart wallet
```

### should add private on variable

```
contracts/liquid-staking/SavETHVaultDeployer.sol

13: address implementation;
```

### should add indexed on events

```
contracts/liquid-staking/SavETHVault.sol

19: event DETHRedeemed(address depositor, uint256 amount);
22: event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

```

```
contracts/liquid-staking/ETHPoolLPFactory.sol

    event ETHWithdrawnByDepositor(address depositor, uint256 amount);
    event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
    event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
    event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

```

### should use fixed compiler version

```
contracts/liquid-staking/SavETHVault.sol

1: pragma solidity ^0.8.13;
```

### should use ! for boolean, not `== false`

contracts/liquid-staking/SavETHVault.sol

```
64: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

```

### unused event

```
contracts/liquid-staking/SavETHVault.sol

121:  event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);

```

### not remove `todo` tag

```
contracts/syndicate/Syndicate.sol

195:  // todo - check else case for any ETH lost

```

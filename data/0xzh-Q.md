## Low Risk and Non-Critical Issues

| |Issue|Instances|
|-|:-|:-:|
| [Low Risk and Non-Critical Issues -1] | Pragma | 21 |
| [Low Risk and Non-Critical Issues -2] | Indexed Events |48 |


## [N-01] Pragma Version

### Locking the pragma ensures that outdated versions of Solidity are not used upon deployment. In such a case, unwanted bugs may appear which negatively affect the contract.

### Proof of Concept
https://swcregistry.io/docs/SWC-103


##### Recommended Mitigation Steps
Lock the pragma version.

### Instances: 21

*Instances (1)*:
```solidity
File: contracts/liquid-staking/OptionalGatekeeperFactory.sol

3:         pragma solidity ^0.8.13;

```

*Instances (2)*:
```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol

1:         pragma solidity ^0.8.13;

```


*Instances (3)*:
```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

1:         pragma solidity ^0.8.13;

```

*Instances (4)*:
```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

3:         pragma solidity ^0.8.0;

```

*Instances (5)*:
```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

3:         pragma solidity ^0.8.13;

```

*Instances (6)*:
```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

1:           pragma solidity ^0.8.13;

```

*Instances (7)*:
```solidity
File: contracts/liquid-staking/GiantLP.sol

1:         pragma solidity ^0.8.13;

```

*Instances (8)*:
```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

1:         pragma solidity ^0.8.13;

```


*Instances (9)*:
```solidity
File: contracts/syndicate/SyndicateFactory.sol

1:       pragma solidity ^0.8.13;

```


*Instances (10)*:
```solidity
File: contracts/liquid-staking/LPToken.sol

1:       pragma solidity ^0.8.13;

```


*Instances (11)*:
```solidity
File: contracts/liquid-staking/LSDNFactory.sol

1:       pragma solidity ^0.8.13;

```


*Instances (12)*:
```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

1:       pragma solidity ^0.8.13;

```


*Instances (13)*:
```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

3:       pragma solidity ^0.8.13;

```


*Instances (14)*:
```solidity
File: contracts/liquid-staking/SavETHVault.sol

1:      pragma solidity ^0.8.13;

```

*Instances (15)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

3:  pragma solidity ^0.8.13;

```

*Instances (16)*:
```solidity
File: contracts/syndicate/Syndicate.sol

1:  pragma solidity ^0.8.13;

```


*Instances (17)*:
```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

1:  pragma solidity ^0.8.13;

```



*Instances (18)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

3:  pragma solidity ^0.8.13;

```

*Instances (19)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

3:  pragma solidity ^0.8.13;

```


*Instances (20)*:
```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

3:  pragma solidity ^0.8.13;

```

*Instances (21)*:
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

3:  pragma solidity ^0.8.13;

```



### [N-02] Indexed Events: Use indexed events as they are less costly compared to non-indexed ones


### Instances: 21

*Instances (1)*:
```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

13:    event ETHDeposited(address indexed sender, uint256 amount);

```


*Instances (2)*:
```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

16:    event LPBurnedForETH(address indexed sender, uint256 amount);

```


*Instances (3)*:
```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

18:   event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);

```

*Instances (4)*:
```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

18:   event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);

```

*Instances (5)*:
```solidity
File: contracts/liquid-staking/SavETHVault.sol

19:   event DETHRedeemed(address depositor, uint256 amount);

```

*Instances (6)*:
```solidity
File: contracts/liquid-staking/SavETHVault.sol

22:  event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

```


*Instances (7)*:
```solidity
File: contracts/liquid-staking/SavETHVault.sol

121:  event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);

```


*Instances (8)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

25:  event ETHDeposited(address sender, uint256 amount);

```


*Instances (9)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

28:  event ETHWithdrawn(address receiver, address admin, uint256 amount);

```


*Instances (10)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

31:  event ERC20Recovered(address admin, address recipient, uint256 amount);

```


*Instances (11)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

34:  event WETHUnwrapped(address admin, uint256 amount);

```

*Instances (12)*:
```solidity
File: contracts/syndicate/Syndicate.sol

42:  event UpdateAccruedETH(uint256 unprocessed);

```

*Instances (13)*:
```solidity
File: contracts/syndicate/Syndicate.sol

45:  event CollateralizedSLOTReCalibrated(bytes BLSPubKey);

```

*Instances (14)*:
```solidity
File: contracts/syndicate/Syndicate.sol

48:   event KNOTRegistered(bytes BLSPubKey);

```

*Instances (15)*:
```solidity
File: contracts/syndicate/Syndicate.sol

51:  event KnotDeRegistered(bytes BLSPubKey);

```

*Instances (16)*:
```solidity
File: contracts/syndicate/Syndicate.sol

54:  event PriorityStakerRegistered(address indexed staker);

```

*Instances (17)*:
```solidity
File: contracts/syndicate/Syndicate.sol

57:  event Staked(bytes BLSPubKey, uint256 amount);

```

*Instances (18)*:
```solidity
File: contracts/syndicate/Syndicate.sol

60: event UnStaked(bytes BLSPubKey, uint256 amount);

```

*Instances (19)*:
```solidity
File: contracts/syndicate/Syndicate.sol

63: event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);

```

*Instances (20)*:
```solidity
File: contracts/syndicate/Syndicate.sol

63: event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);

```


*Instances (21)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

25:   event ETHDeposited(address sender, uint256 amount);

```


*Instances (22)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

28:  event ETHWithdrawn(address receiver, address admin, uint256 amount);

```

*Instances (23)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

31: event ERC20Recovered(address admin, address recipient, uint256 amount);

```


*Instances (24)*:
```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

34: event WETHUnwrapped(address admin, uint256 amount);

```

*Instances (25)*:
```solidity
File: contracts/syndicate/Syndicate.sol

42:  event UpdateAccruedETH(uint256 unprocessed);

```

*Instances (26)*:
```solidity
File: contracts/syndicate/Syndicate.sol

45:  event CollateralizedSLOTReCalibrated(bytes BLSPubKey);

```


*Instances (27)*:
```solidity
File: contracts/syndicate/Syndicate.sol

48:  event KNOTRegistered(bytes BLSPubKey);

```



*Instances (28)*:
```solidity
File: contracts/syndicate/Syndicate.sol

51:  event KnotDeRegistered(bytes BLSPubKey);

```


*Instances (29)*:
```solidity
File: contracts/syndicate/Syndicate.sol

57:   event Staked(bytes BLSPubKey, uint256 amount);

```


*Instances (30)*:
```solidity
File: contracts/syndicate/Syndicate.sol

60:  event UnStaked(bytes BLSPubKey, uint256 amount);

```


*Instances (31)*:
```solidity
File: contracts/syndicate/Syndicate.sol

63:  event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);

```

*Instances (32)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

36:  event WhitelistingStatusChanged(address indexed dao, bool updatedStatus);

```

*Instances (33)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

39:  event NodeRunnerWhitelistingStatusChanged(address indexed nodeRunner, bool updatedStatus);

```



*Instances (34)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

51:  event KnotStaked(bytes _blsPublicKeyOfKnot, address indexed trigerringAddress);

```
   
   
   
*Instances (35)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

54:  event StakehouseCreated(string stakehouseTicker, address indexed stakehouse);

```   
   
*Instances (36)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

57:  event StakehouseJoined(bytes blsPubKey);

```   

*Instances (37)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

63:  event DormantRepresentative(address indexed associatedSmartWallet, address representative);

```   

*Instances (38)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

66:  event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);

```   



*Instances (39)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

69:  event NetworkTickerUpdated(string newTicker);

```   



*Instances (40)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

84: event DAOCommissionUpdated(uint256 old, uint256 newCommission);

```   


*Instances (41)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

87: event NewLSDValidatorRegistered(address indexed nodeRunner, bytes blsPublicKey);

```   


*Instances (42)*:
```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

9:  event ETHReceived(uint256 amount);

```  


*Instances (43)*:
```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

12:  event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);

```  


*Instances (44)*:
```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

12:  event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);

```  


*Instances (45)*:
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

16:  event ETHWithdrawnByDepositor(address depositor, uint256 amount);

```  



*Instances (46)*:
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

19: event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

```  


*Instances (47)*:
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

22:  event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

```  
  

*Instances (47)*:
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

25:   event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

```    
  
   
## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1] | Empty Blocks | 1 |
| [GAS-2] | ABI.Encode | 1 |
| [GAS-3] | Require Strings |  |
| [GAS-5] | Empty Blocks |  |
| [GAS-6] |  |  |




### [GAS-1] EMPTY BLOCKS SHOULD EMIT AN EVENT: If an empty block (constructor or receive statement) does not emit an event, it should be removed to save gas. 


### Instances: 1

*Instances (1)*:
```solidity
File: contracts/liquid-staking/LPToken.sol

28:         constructor() initializer {}

```

*Instances (2)*:
```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

25:         constructor() initializer {}

```

*Instances (3)*:
```solidity
File: contracts/syndicate/Syndicate.sol

39:     event ContractDeployed();

```

*Instances (4)*:
```solidity
File: contracts/syndicate/Syndicate.sol

487:   emit ContractDeployed();

```


*Instances (5)*:
```solidity
File: contracts/syndicate/Syndicate.sol

123: constructor() initializer {}

```



### [GAS-2] ABI.ENCODE(): ABI.ENCODE() is less efficient than ABI.ENCODEPACKED()


### Instances: 1

*Instances (1)*:
```solidity
File: contracts/syndicate/SyndicateFactory.sol

63:       return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));

```







### [GAS-3] Require strings: Amount to more than 32 bytes. The cost of gas and deployment time would be lower if the string in the require statement was smaller and limited to 32 bytes.

*Instances (1)*:
```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

100:      require(
            isTransferApproved(owner(), msg.sender),
            "OwnableSmartWallet: Transfer is not allowed"
        ); // F: [OSW-4]

```

*Instances (2)*:
```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

114:   function setApproval(address to, bool status) external onlyOwner override {
        require(
            to != address(0),
            "OwnableSmartWallet: Approval cannot be set for zero address"
        ); // F: [OSW-2A]
        _setApproval(msg.sender, to, status);
    }

```

*Instances (3)*:
```solidity
File: contracts/liquid-staking/SavETHVault.sol

64:  require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

```


*Instances (4)*:
```solidity
File: contracts/liquid-staking/SavETHVault.sol

122: require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

```

*Instances (4)*:
```solidity
File: contracts/liquid-staking/SavETHVault.sol

133:  require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

```

*Instances (4)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

393:  require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

```


*Instances (4)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

396:  require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");

```

*Instances (4)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

280:   require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

```

*Instances (4)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

291:   require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

```


*Instances (4)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

328: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

```
       
*Instances (4)*:
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

331: require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");

```

        


### [GAS-4] Empty Code Blocks: Empty code blocks should be removed or emit something.

*Instances (1)*:
```solidity
File: contracts/syndicate/Syndicate.sol

39:      event ContractDeployed();

```


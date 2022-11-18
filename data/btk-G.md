Gas saving 1: State variables should be cached in memory variables rather than re-reading them from storage

```solidity 
Lines of code: https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L177-L189

State variable:  numberOfRegisteredKnots 

Deployment gas before: 3219394

Deployment gas used after: 3219178

Gas Saved: 216
```

```solidity 
Lines of code: https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L551

State variable:  totalFreeFloatingShares 

Deployment gas before: 3219394

Deployment gas used after: 3217654

Gas Saved: 1740
```

We can optimize the above functions by caching the state variables to a memory variables.
_______________________________________________________________________________________________________________________________________________________________

Gas saving 2: Add the constant attributes to state variables that [never change](https://dev.to/javier123454321/solidity-gas-optimizations-pt-2-constants-570d)

```solidity 
Lines of code: https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158

State variable:  MODULO  

Deployment gas before: 5395557

Deployment gas used after: 5373903

Gas Saved: 21654
```
_______________________________________________________________________________________________________________________________________________________________

Gas saving 3: ++ Cost less gas then += 1

```solidity 
Lines of code: 
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L782
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L839

State variable:  numberOfKnots 

Use ++numberOfKnots instead of numberOfKnots += 1

Deployment gas before: 5395557

Deployment gas used after: 5393181

Gas Saved: 2376
```
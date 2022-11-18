# Gas optimization 1

Can use unchecked to reduce gas 

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L55-L57

```
  require(idleETH >= _amount, "Come back later or withdraw less ETH");

        idleETH -= _amount;
```
Can use unchecked because after the require, idleETH  always >= _amount 
Propose to change to 
```
require(idleETH >= _amount, "Come back later or withdraw less ETH");

unchecked { 
        idleETH -= _amount;
} 
```
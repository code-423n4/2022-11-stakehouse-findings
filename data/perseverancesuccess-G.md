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

# Gas Optimization 2 

This code is redundant and useless or wrong here because, it transfer the _lpTokens balance of this contract to itself, so it does nothing. 
Please double check or remove this code segment. 

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L183-L185
```
for (uint256 i; i < _lpTokens.length; ++i) {
            _lpTokens[i].transfer(address(this), _lpTokens[i].balanceOf(address(this)));
        }

```
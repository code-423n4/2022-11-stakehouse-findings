GA1: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPTokenFactory.sol#L18-L22
It is better NOT to pass the implementation as an argument, which makes it hard to verify and trust its correctness. Use the following code instead:
```
constructor(address _lpTokenImplementation) {
        lpTokenImplementation =  address(new LPToken());
    }
```

GA2: 
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L805

It should be 
```
 bytes[] memory _blsPublicKeyOfKnots = new bytes[1];
```

GA3: In ``LiquidStakingManager.sol#L805``, in many occasions, it calls the ``execute()`` function in the smart wall to call a function in the ``getTransctionRouter()''. It might be better to define that function (or a wrapper) in OwnableSmartWallet explicit and call it instead.


GA4: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L343
A node runner should be able to withdraw all the balance in the smart wallet instead of a fix 4eth in case some more ETH has been sent to the smart wallet by accident. 

GA5: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L829
`sETH` was received in this line from the stakehouse to the smartWallet first, then transferred to the LiquidStakingManger, and then finally transferred to the syndicate in the account of the smartwallet. The accounting can be done more efficiently, by receiving the `sETH` to the syndicate directly and then gives credit to the corresponding smartwallet/blsPubickey directly. 


 AQ6: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L356
This function provides too much power to Dao, if the dao calls the function, then he can be the node runner of each smart wallet and then call `` withdrawETHForKnot`` to drain each smart wallet. 

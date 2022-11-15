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

 
GA1: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPTokenFactory.sol#L18-L22
It is better NOT to pass the implementation as an argument, which makes it hard to verify and trust its correctness. Use the following code instead:
```
constructor(address _lpTokenImplementation) {
        lpTokenImplementation =  address(new LPToken());
    }
```

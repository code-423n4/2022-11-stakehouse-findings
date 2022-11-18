


## Impact
This performs an unnecessary addition as   totalClaimed

## Proof of Concept
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L317

```
        totalClaimed += unclaimedUserShare;

```

## Recommended Mitigation Steps

 ` -     totalClaimed += unclaimedUserShare; `

 ` +     totalClaimed = unclaimedUserShare; ` 


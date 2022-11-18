# [L-01] Node runner representative can be a contract
## Targets
- https://github.com/code-423n4/2022-11-stakehouse/blob/5f853d055d7aa1bebe9e24fd0e863ef58c004339/contracts/liquid-staking/LiquidStakingManager.sol#L435
## Impact
A node runner can bypass the check and add a representative that's a smart contract, not an EOA, as required by the function.
## Proof of Concept
When a node runner registers BLS keys, they can specify a representative that must be an EOA ([LiquidStakingManager.sol#L435](https://github.com/code-423n4/2022-11-stakehouse/blob/5f853d055d7aa1bebe9e24fd0e863ef58c004339/contracts/liquid-staking/LiquidStakingManager.sol#L435)):
```solidity
require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");
```

However, the check can be easily bypassed since smart contract addresses can be known in advance (`CREATE` and `CREATE2` addresses can be predicted by knowing a nonce (in the case of `CREATE`) or a salt (in the case of `CREATE2`)). Thus, a node runner can specify an address at which a contract will be deployed after the address is registered as a representative.
## Tools Used
Manual review
## Recommended Mitigation Steps
Consider removing the check because there's no reliable way of checking whether an address is an EOA or a smart contract.
# QA Report


# L01 - EOA restriction of wallet representative can be bypassed
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L435

A node operator can call `registerBLSPublicKeys()` to register a node runner to LSD and create a new smart wallet. The protocol only allows EOAs to be registered as `_eoaRepresentative`.

The issue is that this can be easily circumvented. As detailed in the `isContract()` definition by OpenZeppelin:

```
* It is unsafe to assume that an address for which this function returns
* false is an externally-owned account (EOA) and not a contract.
*
* Among others, `isContract` will return false for the following
* types of addresses:
*
*  - an externally-owned account
*  - a contract in construction
*  - an address where a contract will be created
*  - an address where a contract lived, but was destroyed
```

A user can for instance deploy a smart wallet via a factory using CREATE2, call `SELFDESTRUCT` on it.
They can then pass the address of that destructed smart contract as `_eoaRepresentative` to `registerBLSPublicKeys()`. 

The operator can then redeploy the same contract to the same address using a CREATE2 call in their factory.

## Impact

Low


## Tools Used

Manual Analysis


## Mitigation

A potential mitigation would be to change the logic so that the `_eoaRepresentative` would need to call `registerBLSPublicKeys()`, and use a `msg.sender == tx.origin` check to ensure they are an EOA.

# L02 - Lack of coherence for the wallet representative

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L435
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L290-L296
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L309-L320

A node operator can call `registerBLSPublicKeys()` to register a node runner to LSD and create a new smart wallet. The protocol only allows EOAs to be registered as `_eoaRepresentative`.

But in `rotateEOARepresentativeOfNodeRunner()` and `rotateEOARepresentative()`, there is no such check meaning, `_newRepresentative` can be a smart contract.

Note that  the error strings [here](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L296) and [here](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L314) and the [function NATSPEC comment](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L286) explicitly indicate the representative should be an EOA.

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Consider using an `Address.isContract()` check in both functions, but as stated in L01 this does not really mitigate the issue properly.
Another option would be use a two-step rotation, with the current representative setting the pending new representative, and the new representative would call the function setting them as the smart wallet representative - allowing the use of `msg.sender == tx.origin` to ensure they are an EOA.

# L03 - `LiquidStakingManager.dao` can rug node operators with `executeAsSmartWallet()`

```solidity
202: function executeAsSmartWallet(
203:         address _nodeRunner,
204:         address _to,
205:         bytes calldata _data,
206:         uint256 _value
207:     ) external payable onlyDAO {
208:         address smartWallet = smartWalletOfNodeRunner[_nodeRunner];
209:         require(smartWallet != address(0), "No wallet found");
210:         IOwnableSmartWallet(smartWallet).execute(
211:             _to,
212:             _data,
213:             _value
214:         );
215:     }
```

`executeAsSmartWallet()` is meant to enable proxy operations through DAO contracts, but introduces rugging vectors.

Note that as per the docs
```
LSD Networks can be deployed by any Ethereum Account
```

Despite the name, `dao` could be an EOA, making the attack even easier to perform.


A malicious `dao` can wait for a node operator to call `registerBLSPublicKeys()`, which will transfer `amount = 4 ETH * Nvalidators` to the smart wallet, then call `executeAsSmartWallet()` to transfer `amount` to their own account, rugging the node operator.


A bigger possible attack is rugging the staked ETH:

As per the logic in `stake()` the ETH staking deposit to the Ethereum Staking contract is performed by the [node operator wallet](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L755-L767).

When staking withdrawals are enabled, a `dao` can use `executeAsSmartWallet()` to withdraw all the staked ETH, rugging all the depositors.

## Impact

Centralization risk

## Tools Used

Manual Analysis

# L04 - `LiquidStakingManager.dao` can rug node operators with `executeAsSmartWallet()`

`daoCommissionPercentage` is used to [calculate](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L925) the portion of node operator network rewards that are sent to `dao`, when a node runner is calling `claimRewardsAsNodeRunner()`.

There is no timelock to `updateDAORevenueCommission()`, meaning a `dao` can call it with `_commissionPercentage = MODULO` to set `daoCommissionPercentage` to 100%, effectively stealing all the network rewards from node runners.

## Impact

Centralization risk

## Tools Used

Manual Analysis

## Mitigation

Consider either adding a timelock to `updateDAORevenueCommission()`, or add an upper boundary to it, so that `daoCommissionPercentage` is always reasonable.


# L05 - `LiquidStakingManager.receive` can lead to user losing funds

The `receive()` function is empty. If an account mistakenly sends `ETH` to a `LiquidStakingManager`, they have no way to retrieve that amount.

It will later be taken into account for rewards calculation [here](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L400)

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

The `LiquidStakingManager.receive()` function should check `msg.sender` to ensure the call was triggered by a rightful address to ensure they are staking yield/rewards, and not an arbitrary transfer from a random EOA.

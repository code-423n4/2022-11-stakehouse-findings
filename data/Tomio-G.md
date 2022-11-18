1.
Title: Reduce the size of error messages (Long revert Strings)

Impact:
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Proof of Concept:
[OwnableSmartWallet.sol#L35](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L35)
[OwnableSmartWallet.sol#L102](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L102)
[OwnableSmartWallet.sol#L117](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L117)
[SavETHVault.sol#L84](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L84)
[StakingFundsVault.sol#L79](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79)
[StakingFundsVault.sol#L114](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114)

Recommended Mitigation Steps:
Consider shortening the revert strings to fit in 32 bytes
________________________________________________________________________

2.
Title: Consider delete empty block or emit something

impact:
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

Proof of Concept:
[OwnableSmartWallet.sol#L148](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L148)
[SyndicateRewardsProcessor.sol#L98](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98)
________________________________________________________________________

3.
Title: abi.encode() is less efficient than abi.encodePacked()

Proof of Concept:
[SyndicateFactory.sol#L63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L63)
________________________________________________________________________

4.
Title: Unchecking arithmetics operations that can't underflow/overflow

Proof of Concept:
[GiantPoolBase.sol#L57](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L57) Should be unchecked due to `require` condition in L#55

Recommended Mitigation Steps:
Use `unchecked` can save gas
________________________________________________________________________

5.
Title: Boolean comparison

Proof of Concept:
[SavETHVault.sol#L84](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L84)
[StakingFundsVault.sol#L79](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79)
[StakingFundsVault.sol#L114](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114)
[Syndicate.sol#L611-L612](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L611-L612)

Recommended Mitigation Steps:
Change to:
`require(!x, "")` instead of `require(x == false, "")`
and
`require(x, "")` instead of `require(x == true, "")`
________________________________________________________________________

6.
Title: function burnLPToken() gas improvement on returning value

Proof of Concept:
[SavETHVault.sol#L150](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L150)

Recommended Mitigation Steps:
by set `redemptionValue` in returns L#126 and delete L#150 can save gas

```
function burnLPToken(LPToken _lpToken, uint256 _amount) public nonReentrant returns (uint256 redemptionValue) { //@audit-info: set here
```
________________________________________________________________________

7.
Title: Comparison operators

Proof of Concept:
[StakingFundsVault.sol#L240](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L240)

Recommended Mitigation Steps:
Change to:
`_amount > 3 ether` can save some gas
________________________________________________________________________

8.
Title: Gas improvement on returning value

Proof of Concept:
[StakingFundsVault.sol#L275](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L275)
[StakingFundsVault.sol#L285](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L285)

Recommended Mitigation Steps:
by set `totalAccumulated` in returns L#274 and delete L#275 can save gas

```
function batchPreviewAccumulatedETHByBLSKeys(address _user, bytes[] calldata _blsPubKeys) external view returns (uint256 totalAccumulated) { //@audit-info: set here

```
________________________________________________________________________

9.
Title: Gas optimization to dividing by 2

Proof of Concept:
[Syndicate.sol#L378](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L378)

Recommended Mitigation Steps:
Replace `/ 2` with `>> 1`

Reference: [here](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
________________________________________________________________________

10.
Title: `>=` is cheaper than `>`

Impact:
Strict inequalities (`>`) are more expensive than non-strict ones (`>=`). This is due to some supplementary checks (ISZERO, 3 gas)

Proof of Concept:
[Syndicate.sol#L634](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L634)

Recommended Mitigation Steps:
Consider using `>=` instead of `>` to avoid some opcodes
________________________________________________________________________

11.
Title: Using multiple `require` instead `&&` can save gas

Proof of Concept:
[LiquidStakingManager.sol#L357](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L357)

Recommended Mitigation Steps:
Change to:

```
	require(_new != address(0), "New is zero or current");
	require(_current != _new, "New is zero or current");
```
________________________________________________________________________

12.
Title: Unnecessary MSTORE

Proof of Concept:
[SyndicateRewardsProcessor.sol#L58](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L58)

Recommended Mitigation Steps:
use `_balance` directly instead of caching it to `balance` and delete L#58
________________________________________________________________________
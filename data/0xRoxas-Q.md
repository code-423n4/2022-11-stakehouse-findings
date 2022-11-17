# QA Report
## Found [3]

### [NC-01] Pragma is not Forced (Floating Pragma)

https://swcregistry.io/docs/SWC-103

#### Findings:

*/contracts/liquid-staking/OptionalGatekeeperFactory.sol*
Line(s): [3](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L3)
```solidity
3:	pragma solidity ^0.8.13;
```

*/contracts/liquid-staking/OptionalHouseGatekeeper.sol*
Line(s): [1](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L1)
```solidity
1:	pragma solidity ^0.8.13;
```

*/contracts/liquid-staking/SavETHVaultDeployer.sol*
Line(s): [1](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L1)
```solidity
1:	pragma solidity ^0.8.13;
```

...

### [NC-02] Confusing _ Notation Format

Although commented on [L157](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L157) using the format `100_00000` instead of  `10_000_000` will confuse readers.

#### Findings:

*/contracts/liquid-staking/LiquidStakingManager.sol*
Line(s): [158](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158)
```solidity
157:	/// @notice 100% to 5 decimal places
158:	uint256 public MODULO = 100_00000;
```

### [NC-03] Magic Number Used

#### Findings:

A magic number `0.5 ether` is used. Consider making a variable in `0.5 ether`'s place.

*/contracts/liquid-staking/GiantSavETHVaultPool.sol*
Line(s): [127](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L127)
```solidity
127:	require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
```

*/contracts/liquid-staking/GiantMevAndFeesPool.sol*
Line(s): [116](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L116)

```solidity
116:	require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
```

A magic number `48` is used. Consider making a variable in `48`'s place.

*/contracts/liquid-staking/ETHPoolLPFactory.sol*
Line(s): [88/89](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L88-L89), [112](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L112)
```solidity
88:	require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");
89:	require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");
112:	require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");
```

A magic number `24 ether` is used. Consider making a variable in `24 ether`'s place. Another magic number `30 minutes` is also used.

*/contracts/liquid-staking/SavETHVault.sol*
Line(s): [141](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L141), [161](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L161), [174](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L174), [204](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L204), [244](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L244)
```solidity
141:	bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;
161:	require(dETHBalance >= 24 ether, "Nothing to withdraw");
174:	redemptionValue = (dETHDetails.savETHBalance * _amount) / 24 ether;
204:	require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
244:	maxStakingAmountPerValidator = 24 ether;
```

A magic number `4 ether` is used. Consider making a variable in `4 ether`'s place. Another magic number `30 minutes` is also used.

*/contracts/liquid-staking/StakingFundsVault.sol*
Line(s): [58](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L58), [184](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#184), [230](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L230), [240](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L240), [380](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L380)
```solidity
58:	totalShares += 4 ether;
184:	require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");
240:	require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
230:	require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
380:	maxStakingAmountPerValidator = 4 ether;
```

A magic number `12 ether` is used. Consider making a variable in `12 ether`'s place. Another magic number `1 gwei` is also used, aswell as `4 ether`.

*/contracts/syndicate/Syndicate.sol*
Line(s): [215](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L215), [221/223](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L221-L223), [362](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L362), [429](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L429), [431](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L431), [504](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L504), [522](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L522), [546](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L546)
```solidity
215:	if (_sETHAmount < 1 gwei) revert FreeFloatingStakeAmountTooSmall();
221:	if (totalStaked == 12 ether) revert KnotIsFullyStakedWithFreeFloatingSlotTokens();
223:	if (_sETHAmount + totalStaked > 12 ether) revert InvalidStakeAmount();
362:	if (stakedBal < 1 gwei) revert FreeFloatingStakeAmountTooSmall();
429:	if (currentSlashedAmount < 4 ether) {
431:	balance * unprocessedForKnot / (4 ether - currentSlashedAmount);
504:	if (currentSlashedAmount < 4 ether) {
522:	balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);
546:	return (_ethSinceLastUpdate * PRECISION) / (numberOfRegisteredKnots * 4 ether);
```

A magic number `4 ether` is used. Consider making a variable in `4 ether`'s place. Another magic number `24 ether` is also used.

*/contracts/liquid-staking/LiquidStakingManager.sol*
Line(s): [333](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L333), [343](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L343), [434](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L434), [749](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L749), [752](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L752), [936](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L936), [940](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L940), [944](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L944)
```solidity
333:	require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");
343:	4 ether
434:	require(msg.value == len * 4 ether, "Insufficient ether provided");
749:	savETHVault.withdrawETHForStaking(smartWallet, 24 ether);
752:	stakingFundsVault.withdrawETH(smartWallet, 4 ether);
936:	require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
940:	require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
944:	require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
```
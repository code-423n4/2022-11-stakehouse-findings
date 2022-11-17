# Gas Report
## Optimizations found [10]

### [G-01] Use Bit Shift Operators for MUL/DIV/MOD of Powers of 2 [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#1--use-of-bit-shift-operators)

#### Findings:

*/contracts/syndicate/Syndicate.sol*
Line(s): [378](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L378)
```solidity
378:	return ethPerKnot / 2;
```
**suggested change**
```solidity
378:	return ethPerKnot >> 1;
```

### [G-02] Remove Manual True/False Checks to Save Deployment Costs 



#### Findings:

*/contracts/liquid-staking/GiantSavETHVaultPool.sol*
Line(s): [150](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L150)
```solidity
150:	vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
```

*/contracts/liquid-staking/SavETHVault.sol*
Line(s): [64](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L64), [84](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L84)
```solidity
64:	require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
84:	require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```

*/contracts/liquid-staking/StakingFundsVault.sol*
Line(s): [79](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79), [114](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114), [205](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L205)
```solidity
79:	require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
114:	require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
205:	liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
```

*/contracts/syndicate/Syndicate.sol*
Line(s): [611](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L611), [612](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L612)
```solidity
611:	if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();
612:	if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();
```

*/contracts/liquid-staking/LiquidStakingManager.sol*
Line(s): [291](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L291), [328](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L328), [332](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L332), [393](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L393), [436](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L436), [437](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L437), [469](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L469), [541](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L541), [589](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L589), [688](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L688)
```solidity
291:	require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
328:	require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
332:	require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
393:	require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
436:	require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
437:	require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
469:	require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
541:	require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
589:	require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
688:	require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
```


### [G-03] ++i is cheaper than (i++)/(i+=1) [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-12-i-costs-less-gas-compared-to-i-or-i--1)

#### Findings:

*/contracts/liquid-staking/LiquidStakingManager.sol*
Line(s): [770](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L770), [782](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L782), [839](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L839)
```solidity
770:	stakedKnotsOfSmartWallet[smartWallet] += 1;
782:	numberOfKnots += 1;
839:	numberOfKnots += 1;
```

### [G-04] --i is cheaper than (i--)/(i-=1) [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-12-i-costs-less-gas-compared-to-i-or-i--1)

#### Findings:

*/contracts/syndicate/Syndicate.sol*
Line(s): [624](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L624)
```solidity
624:	numberOfRegisteredKnots -= 1;
```

*/contracts/liquid-staking/LiquidStakingManager.sol*
Line(s): [615](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L615)
```solidity
615:	stakedKnotsOfSmartWallet[smartWallet] -= 1;
```
### [G-05] Use nested if instead of && [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#6--use-nested-if-and-avoid-multiple-check-combinations)

#### Findings:

*/contracts/liquid-staking/StakingFundsVault.sol*
Line(s): [215](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L215)
```solidity
215:	if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {
```

*/contracts/syndicate/Syndicate.sol*
Line(s): [218](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L218), [500](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L500), [533](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L533), [588](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L588)
```solidity
218:	if (block.number < priorityStakingEndBlock && !isPriorityStaker[_onBehalfOf]) revert NotPriorityStaker();
500:	if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {
533:	if (!isActive && !isNoLongerPartOfSyndicate[_blsPubKey]) {
588:	if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();
```

*/contracts/liquid-staking/LiquidStakingManager.sol*
Line(s): [371](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L371), [700](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L700), [717](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L717)
```solidity
371:	if (msg.sender == dao && _wasPreviousNodeRunnerMalicious) {
700:	if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) {
717:	else if(_isEnabled && smartWalletRepresentative[_smartWallet] == address(0)) {
```


### [G-06] Break Apart Require Statments Instead of && [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-11-splitting-require-statements-that-use--saves-gas)

#### Findings:

*/contracts/liquid-staking/LiquidStakingManager.sol*
Line(s): [357](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L357)
```solidity
357:	require(_new != address(0) && _current != _new, "New is zero or current");
```

### [G-07] Unused Variables Can Be Removed to Save Gas


*/contracts/liquid-staking/GiantPoolBase.sol*
Line(s): [31](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L31)

```solidity
31:	LSDNFactory public liquidStakingDerivativeFactory;
```


### [G-08] Variable Can Be Made `Immutable` To Save Gas

Variables that are only initialized in the constructor can be made immutable to save on gas.

#### Findings:

*/contracts/liquid-staking/OptionalHouseGatekeeper.sol*
Line(s): [12](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L12)

```solidity
12:	ILiquidStakingManager public liquidStakingManager;
```
**suggested change**
```solidity
12:	ILiquidStakingManager public immutable liquidStakingManager;
```

*/contracts/liquid-staking/SavETHVaultDeployer.sol*
Line(s): [13](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L13)

```solidity
13:	address implementation;
```
**suggested change**
```solidity
13:	address immutable implementation;
```

*/contracts/liquid-staking/StakingFundsVaultDeployer.sol*
Line(s): [13](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L13)

```solidity
13:	address implementation;
```
**suggested change**
```solidity
13:	address immutable implementation;
```

*/contracts/liquid-staking/LPTokenFactory.sol*
Line(s): [15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15)

```solidity
13:	address public lpTokenImplementation;
```
**suggested change**
```solidity
13:	address public immutable lpTokenImplementation;
```

*/contracts/liquid-staking/GiantLP.sol*
Line(s): [11](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L11), [14](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L14)

```solidity
11:	address public pool;
14:	ITransferHookProcessor public transferHookProcessor;
```
**suggested change**
```solidity
11:	address public immutable pool;
14:	ITransferHookProcessor public immutable transferHookProcessor;
```

*/contracts/syndicate/SyndicateFactory.sol*
Line(s): [13](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L13)

```solidity
13:	address public syndicateImplementation;
```
**suggested change**
```solidity
13:	address public immutable syndicateImplementation;
```

*/contracts/liquid-staking/LSDNFactory.sol*
Line(s): [15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L15), [21](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L21), [24](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L24), [27](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L27), [30](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L30), [33](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L33), [36](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L36)

```solidity
15:	address public liquidStakingManagerImplementation;
21:	address public lpTokenFactory;
24:	address public smartWalletFactory;
27:	address public brand;
30:	address public savETHVaultDeployer;
33:	address public stakingFundsVaultDeployer;
36:	address public optionalGatekeeperDeployer;
```
**suggested change**
```solidity
15:	address public immutable liquidStakingManagerImplementation;
21:	address public immutable  lpTokenFactory;
24:	address public immutable  smartWalletFactory;
27:	address public immutable  brand;
30:	address public immutable  savETHVaultDeployer;
33:	address public immutable  stakingFundsVaultDeployer;
36:	address public immutable  optionalGatekeeperDeployer;
```

### [G-09] Use `unchecked` For Arithmetic That Cannot Overflow 

Arithmetic is performed that cannot overflow / underflow based on a previous require. Adding an `unchecked` brace around these occurances will save gas (Solidity will not force an underflow / overflow).

**NOTE:** Findings show the check before each line that allows the second line to be `unchecked`.  Only the line following the check should be unchecked (NOT the check itself).

```solidity
unchecked {
	//Code
}
```

*/contracts/liquid-staking/GiantPoolBase.sol*
Line(s): [55](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L55) / [57](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L57)

```solidity
55:	require(idleETH >= _amount, "Come back later or withdraw less ETH");
57:	idleETH -= _amount;
```

### [G-10] Using `unchecked` In For Loop With uint256 Iterator

The iterator will not overflow, `unchecking` the iterator saves gas in loops.

**Example:**
**from**
```solidity
for (uint256 i; i < amountOfTokens; ++i) {
	//Code
}
```
**to** 
```solidity
for (uint256 i; i < amountOfTokens;) {
	//Code
	unchecked {
		++i;
	}
}
```
*/contracts/liquid-staking/GiantPoolBase.sol*
Line(s): [76](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L76)

```solidity
76:	for (uint256 i; i < amountOfTokens; ++i) {
```

*/contracts/liquid-staking/GiantSavETHVaultPool.sol*
Line(s): [42](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L42), [78](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L78),  [82](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L82), [128](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L128), [146](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L146), [148](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L148)

```solidity
42:	for (uint256 i; i < numOfSavETHVaults; ++i) {
78:	for (uint256 i; i < numOfVaults; ++i) {
82:	for (uint256 j; j < _lpTokens[i].length; ++j) {
128:	for (uint256 i; i < numOfRotations; ++i) {
146:	for (uint256 i; i < numOfVaults; ++i) {
148:	for (uint256 j; j < _lpTokens[i].length; ++j) {
```

*/contracts/liquid-staking/ETHPoolLPFactory.sol*
Line(s): [63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L63)

```solidity
63:	for (uint256 i; i < numOfRotations; ++i) {
```

*/contracts/liquid-staking/GiantMevAndFeesPool.sol*
Line(s): [38](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L38), [64](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L64), [90](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L90), [117](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#117), [135](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L135), [183](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L183)

```solidity
38:	for (uint256 i; i < numOfVaults; ++i) {
64:	for (uint256 i; i < numOfVaults; ++i) {
90:	for (uint256 i; i < _stakingFundsVaults.length; ++i) {
117:	for (uint256 i; i < numOfRotations; ++i) {
135:	for (uint256 i; i < numOfVaults; ++i) {
183:	for (uint256 i; i < _lpTokens.length; ++i) {
```

*/contracts/liquid-staking/SavETHVault.sol*
Line(s): [63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L63), [103](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L103), [116](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L116)

```solidity
63:	for (uint256 i; i < numOfValidators; ++i) {
103:	for (uint256 i; i < numOfTokens; ++i) {
116:	for (uint256 i; i < numOfTokens; ++i) {
```

*/contracts/liquid-staking/StakingFundsVault.sol*
Line(s): [78](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L78), [152](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L152), [166](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L166), [203](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L203), [266](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L266), [276](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L276), [286](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L286)

```solidity
78:	for (uint256 i; i < numOfValidators; ++i) {
152:	for (uint256 i; i < numOfTokens; ++i) {
166:	for (uint256 i; i < numOfTokens; ++i) {
203:	for (uint256 i; i < _blsPubKeys.length; ++i) {
266:	for (uint256 i; i < _blsPublicKeys.length; ++i) {
276:	for (uint256 i; i < _blsPubKeys.length; ++i) {
286:	for (uint256 i; i < _token.length; ++i) {
```

*/contracts/syndicate/Syndicate.sol*
Line(s) [211](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L211), [259](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L259), [301](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L301)
, [346](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L346), [420](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L420), [513](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L513), [560](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L560), [585](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L585), [598](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L598), [648](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L648)

```solidity
211:	for (uint256 i; i < _blsPubKeys.length; ++i) {
259:	for (uint256 i; i < _blsPubKeys.length; ++i) {
301:	for (uint256 i; i < _blsPubKeys.length; ++i) {
346:	for (uint256 i; i < _blsPubKeys.length; ++i) {
420:	for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
513:	for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
560:	for (uint256 i; i < knotsToRegister; ++i) {
585:	for (uint256 i; i < _priorityStakers.length; ++i) {
598:	for (uint256 i; i < _blsPublicKeys.length; ++i) {
648:	for (uint256 i; i < _blsPubKeys.length; ++i) {
```

*/contracts/liquid-staking/LiquidStakingManager.sol*
Line(s): [392](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L392), [465](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L465), [538](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L538), [587](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L587)

```solidity
392:	for(uint256 i; i < _blsPubKeys.length; ++i) {
465:	for(uint256 i; i < len; ++i) {
538:	for (uint256 i; i < numOfValidators; ++i) {
587:	for (uint256 i; i < numOfKnotsToProcess; ++i) {
```
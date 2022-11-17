## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 1 | - |
| [GAS&#x2011;2](#GAS&#x2011;2) | Empty Blocks Should Be Removed Or Emit Something | 4 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas | 45 | - |
| [GAS&#x2011;4](#GAS&#x2011;4) | Splitting `require()` Statements That Use `&&` Saves Gas | 1 | 9 |
| [GAS&#x2011;5](#GAS&#x2011;5) | `abi.encode()` is less efficient than `abi.encodepacked()` | 1 | 100 |
| [GAS&#x2011;6](#GAS&#x2011;6) | Don't compare boolean expressions to boolean literals | 14 | 126 |
| [GAS&#x2011;7](#GAS&#x2011;7) | Multiplication/division By Two Should Use Bit Shifting | 3 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It | 4 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | Use calldata instead of memory for function parameters | 7 | 2100 |
| [GAS&#x2011;10](#GAS&#x2011;10) | Use assembly to check for `address(0)` | 5 | - |

Total: 85 contexts over 10 issues

## Gas Optimizations


#### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
require(transferResult, "Failed to transfer");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L412

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L416




### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```solidity
100: function _onDepositETH() internal virtual {}
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L100

```solidity
103: function _onWithdraw(LPToken[] calldata _lpTokens) internal virtual {}
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L103

```solidity
629: receive() external payable {}
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L629

```solidity
98: receive() external payable {}
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98




### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas

#### <ins>Proof Of Concept</ins>


```solidity
79: require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L79

```solidity
122: require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L122

```solidity
130: require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L130

```solidity
55: require(idleETH >= _amount, "Come back later or withdraw less ETH");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L55

```solidity
95: require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L95

```solidity
89: require(vault.isDETHReadyForWithdrawal(address(token)), "dETH is not ready for withdrawal");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L89

```solidity
256: require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L256

```solidity
257: require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L257

```solidity
258: require(numberOfKnots == 0, "Cannot change ticker once house is created");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L258

```solidity
268: require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L268

```solidity
280: require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L280

```solidity
291: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L291

```solidity
296: require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L296

```solidity
314: require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L314

```solidity
328: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L328

```solidity
331: require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L331

```solidity
332: require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L332

```solidity
393: require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L393

```solidity
396: require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L396

```solidity
433: require(len == _blsSignatures.length, "Unequal number of array values");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L433

```solidity
435: require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L435

```solidity
437: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L437

```solidity
455: require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L455

```solidity
469: require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L469

```solidity
541: require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L541

```solidity
589: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L589

```solidity
662: require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L662

```solidity
663: require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L663

```solidity
936: require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L936

```solidity
939: require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L939

```solidity
940: require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L940

```solidity
943: require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L943

```solidity
944: require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L944

```solidity
50: require(msg.sender == address(liquidStakingManager), "Not the savETH vault manager");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L50

```solidity
64: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L64

```solidity
84: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L84

```solidity
90: require(msg.value == _amount, "Must provide correct amount of ETH");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L90

```solidity
204: require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L204

```solidity
205: require(address(this).balance >= _amount, "Insufficient withdrawal amount");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L205

```solidity
79: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L79

```solidity
114: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L114

```solidity
120: require(msg.value == _amount, "Must provide correct amount of ETH");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L120

```solidity
154: require(address(token) != address(0), "No ETH staked for specified BLS key");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L154

```solidity
240: require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L240

```solidity
267: require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L267




### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(_new != address(0) && _current != _new, "New is zero or current");` use:

```
	require(_new != address(0));
	require(_current != _new);
```

#### <ins>Proof Of Concept</ins>

```solidity
357: require(_new != address(0) && _current != _new, "New is zero or current");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L357





### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
63: return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol#L63





### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Don't compare boolean expressions to boolean literals

For cases of: `if (<x> == true)`, use `if (<x>)` instead
For cases of: `if (<x> == false)`, use `if (!<x>)` instead

#### <ins>Proof Of Concept</ins>

```solidity
291: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L291

```solidity
328: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L328

```solidity
332: require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L332

```solidity
393: require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L393

```solidity
436: require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L436

```solidity
437: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L437

```solidity
469: require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L469

```solidity
541: require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L541

```solidity
589: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L589

```solidity
688: require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L688

```solidity
64: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L64

```solidity
84: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L84

```solidity
79: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L79

```solidity
114: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L114





### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Multiplication/division By Two Should Use Bit Shifting

<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

#### <ins>Proof Of Concept</ins>


```solidity
870: sETH.approve(syndicate, (2 ** 256) - 1);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L870

```solidity
174: redemptionValue = (dETHDetails.savETHBalance * _amount) / 24 ether;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L174

```solidity
378: return ethPerKnot / 2;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L378





### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Help The Optimizer By Saving A Storage Variable's Reference Instead Of Repeatedly Fetching It

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

#### <ins>Proof Of Concept</ins>


```solidity
615: stakedKnotsOfSmartWallet[smartWallet] -= 1;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L615

```solidity
617: if(stakedKnotsOfSmartWallet[smartWallet] == 0) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L617

```solidity
618: _authorizeRepresentative(smartWallet, smartWalletDormantRepresentative[smartWallet], true);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L618

```solidity
563: if (isKnotRegistered[blsPubKey]) revert KnotIsAlreadyRegistered();
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L563






### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of
memory is more optimal.

Consider the following generic example:
```
contract C {
	function add(uint[] memory arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above example, the dynamic array arr has the storage location
memory. When the function gets called externally, the array values are
kept in calldata and copied to memory during ABI decoding (using the
opcode calldataload and mstore). And during the for loop, arr[i]
accesses the value in memory using a mload. However, for the above
example this is inefficient. Consider the following snippet instead:
```
contract C {
	function add(uint[] calldata arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above snippet, instead of going via memory, the value is directly
read from calldata using calldataload. That is, there are no
intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with
copying value from calldata to memory in a for loop. Each iteration
would cost at least 60 gas. In the latter example, this can be
completely avoided. This will also reduce the number of instructions and
therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument
is only read.

Note that in older Solidity versions, changing some function arguments
from memory to calldata may cause "unimplemented feature error".
This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples
Note: The following pattern is prevalent in the codebase:
```
function f(bytes memory data) external {
	(...) = abi.decode(data, (..., types, ...));
}
```
Here, changing to bytes calldata will decrease the gas. The total
savings for this change across all such uses would be quite
significant.

#### <ins>Proof Of Concept</ins>


```solidity
function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L355

```solidity
function _claimFundsFromSyndicateForDistribution(address _syndicate, bytes[] memory _blsPubKeys) internal {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L360

```solidity
function initialize(
        address _contractOwner,
        uint256 _priorityStakingEndBlock,
        address[] memory _priorityStakers,
        bytes[] memory _blsPubKeysForSyndicateKnots
    ) external virtual override initializer {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L129

```solidity
function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L343

```solidity
function _initialize(
        address _contractOwner,
        uint256 _priorityStakingEndBlock,
        address[] memory _priorityStakers,
        bytes[] memory _blsPubKeysForSyndicateKnots
    ) internal {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L469

```solidity
function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots) internal {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L555

```solidity
function _addPriorityStakers(address[] memory _priorityStakers) internal {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L583






### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Use assembly to check for `address(0)`

Save 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
	if iszero(_addr) {
		mstore(0x00, "AddressZero")
		revert(0x00, 0x20)
	}
}
```

#### <ins>Proof Of Concept</ins>


```solidity
240: require(_newAddress != address(0), "Zero address");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L240

```solidity
290: require(_newRepresentative != address(0), "Zero address");
294: require(smartWallet != address(0), "No smart wallet");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#290

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#294

```solidity
require(_nodeRunner != address(0), "Zero address");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L685

```solidity
        require(
            initialOwner != address(0),
            "OwnableSmartWallet: Attempting to initialize with zero address owner"
        );
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L33-L36

```solidity
require(owner != address(0), 'Wallet cannot be address 0');
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L37






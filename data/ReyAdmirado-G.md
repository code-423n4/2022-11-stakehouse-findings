# Gas


| | issue |
| ----------- | ----------- |
| 1 | [State variables only set in the constructor should be declared immutable.](#1-state-variables-only-set-in-the-constructor-should-be-declared-immutable) |
| 2 | [state variables can be packed into fewer storage slots](#2-state-variables-can-be-packed-into-fewer-storage-slots) |
| 3 | [Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate](#3-multiple-addressid-mappings-can-be-combined-into-a-single-mapping-of-an-addressid-to-a-struct-where-appropriate) |
| 4 | [state variables should be cached in stack variables rather than re-reading them from storage](#4-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) |
| 5 | [Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement](#5-add-unchecked--for-subtractions-where-the-operands-cannot-underflow-because-of-a-previous-require-or-if-statement) |
| 6 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#6-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 7 | [not using the named return variables when a function returns, wastes deployment gas](#7-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 8 | [can make the variable outside the loop to save gas](#8-can-make-the-variable-outside-the-loop-to-save-gas) |
| 9 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#9-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 10 | [require()/revert() strings longer than 32 bytes cost extra gas](#10-requirerevert-strings-longer-than-32-bytes-cost-extra-gas) |
| 11 | [splitting require() statements that use `&&` saves gas](#11-splitting-require-statements-that-use--saves-gas) |
| 12 | [using `calldata` instead of `memory` for read-only arguments in external functions saves gas](#12-using-calldata-instead-of-memory-for-read-only-arguments-in-external-functions-saves-gas) |
| 13 | [internal functions only called once can be inlined to save gas](#13-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 14 | [internal or private functions not called by the contract or inherited ones should be removed to save deployment gas](#14-internal-or-private-functions-not-called-by-the-contract-or-inherited-ones-should-be-removed-to-save-deployment-gas) |
| 15 | [bytes constants are more efficient than string constants](#15-bytes-constants-are-more-efficient-than-string-constants) |
| 16 | [public functions not called by the contract should be declared external instead](#16-public-functions-not-called-by-the-contract-should-be-declared-external-instead) |
| 17 | [should use arguments instead of state variable](#17-should-use-arguments-instead-of-state-variable) |
| 18 | [Don’t compare boolean expressions to boolean literals](#18-dont-compare-boolean-expressions-to-boolean-literals) |

## 1. State variables only set in the constructor should be declared immutable.

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmaccess (100 gas) with a PUSH32 (3 gas).

- [GiantLP.sol#L11](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L11)
- [GiantLP.sol#L14](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L14)

- [OptionalHouseGatekeeper.sol#L12](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L12)

- [StakingFundsVaultDeployer.sol#L13](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L13)

- [LPTokenFactory.sol#L15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15)

- [LSDNFactory.sol#L15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L15)
- [LSDNFactory.sol#L18](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L18)
- [LSDNFactory.sol#L21](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L21)
- [LSDNFactory.sol#L24](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L24)
- [LSDNFactory.sol#L27](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L27)
- [LSDNFactory.sol#L30](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L30)
- [LSDNFactory.sol#L33](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L33)
- [LSDNFactory.sol#L36](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L36)

- [SyndicateFactory.sol#L13](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L13)


## 2. state variables can be packed into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper.

put it near one of the state vars that have less than 32 bytes (like address ones)
- [LiquidStakingManager.sol#L120](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L120)


## 3. Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. 

consider putting them with other mappings that with them would be under 32byte to save 20000 gas each
- [LiquidStakingManager.sol#L123](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L123)
- [LiquidStakingManager.sol#L149](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L149)


here 
- [Syndicate.sol#L96](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L96)
- [Syndicate.sol#L99](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L99)

more possibilities here if checked carefully 
- [Syndicate.sol#L90-L120](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L90-L120)


## 4. state variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 

because it if statement will be true cache `transferHookProcessor` first 
- [GiantLP.sol#L40](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L40)
- [GiantLP.sol#L46](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L46)

cache before if statement `lpTokenETH`
- [GiantMevAndFeesPool.sol#L151-L166](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L151-L166)

cache `enableWhitelisting` before emit
- [LiquidStakingManager.sol#L270](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L270)

cache `smartWalletRepresentative[smartWallet]`before if statement, highly possible gas save
- [LiquidStakingManager.sol#L454](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L454)

`gatekeeper`
- [LiquidStakingManager.sol#L854](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L854)

`stakehouse`
- [LiquidStakingManager.sol#L843](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L843)


## 5. Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
this will stop the check for overflow and underflow so it will save gas

- [Syndicate.sol#L431](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L431)
- [Syndicate.sol#L522](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L522)


## 6. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas

- [GiantPoolBase.sol#L39](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L39)
- [GiantPoolBase.sol#L57](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L57)

- [LiquidStakingManager.sol#L615](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L615)
- [LiquidStakingManager.sol#L770](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L770)
- [LiquidStakingManager.sol#L782](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L782)
- [LiquidStakingManager.sol#L839](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L839)

- [GiantSavETHVaultPool.sol#L46](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L46)

- [GiantMevAndFeesPool.sol#L40](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L40)

- [StakingFundsVault.sol#L58](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L58)

- [SyndicateRewardsProcessor.sol#L65](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L65)
- [SyndicateRewardsProcessor.sol#L85](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L85)

- [Syndicate.sol#L185](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L185)
- [Syndicate.sol#L190](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L190)
- [Syndicate.sol#L225](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L225)
- [Syndicate.sol#L226](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L226)
- [Syndicate.sol#L227](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L227)
- [Syndicate.sol#L317](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L317)
- [Syndicate.sol#L511](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L511)
- [Syndicate.sol#L521](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L521)
- [Syndicate.sol#L558](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L558)
- [Syndicate.sol#L658](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L658)

- [Syndicate.sol#L269](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L269)
- [Syndicate.sol#L272](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L272)
- [Syndicate.sol#L273](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L273)
- [Syndicate.sol#L621](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L621)
- [Syndicate.sol#L624](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L624)


## 7. not using the named return variables when a function returns, wastes deployment gas

also making 2 stack variables without being used
- [LiquidStakingManager.sol#L921](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L921)


## 8. can make the variable outside the loop to save gas

- [GiantPoolBase.sol#L77](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L77)
- [GiantPoolBase.sol#L78](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L78)

- [GiantSavETHVaultPool.sol#L43](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L43)
- [GiantSavETHVaultPool.sol#L48](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L48)
- [GiantSavETHVaultPool.sol#L79](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L79)
- [GiantSavETHVaultPool.sol#L83](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L83)
- [GiantSavETHVaultPool.sol#L84](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L84)
- [GiantSavETHVaultPool.sol#L147](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L147)

- [SavETHVault.sol#L70](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L70)
- [SavETHVault.sol#L104](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L104)

- [StakingFundsVault.sol#L85](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L85)
- [StakingFundsVault.sol#L96](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L96)
- [StakingFundsVault.sol#L153](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L153)
- [StakingFundsVault.sol#L228](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L228)
- [StakingFundsVault.sol#L277](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L277)
- [StakingFundsVault.sol#L288](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L288)


## 9. `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [ETHPoolLPFactory.sol#L63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L63)

- [GiantMevAndFeesPool.sol#L38](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L38)
- [GiantMevAndFeesPool.sol#L64](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L64)
- [GiantMevAndFeesPool.sol#L90](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L90)
- [GiantMevAndFeesPool.sol#L117](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L117)
- [GiantMevAndFeesPool.sol#L146](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L146)
- [GiantMevAndFeesPool.sol#L148](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L148)

- [GiantPoolBase.sol#L76](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L76)

- [GiantSavETHVaultPool.sol#L42](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L42)
- [GiantSavETHVaultPool.sol#L78](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L78)
- [GiantSavETHVaultPool.sol#L82](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L82)
- [GiantSavETHVaultPool.sol#L128](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L128)
- [GiantSavETHVaultPool.sol#L146](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L146)
- [GiantSavETHVaultPool.sol#L148](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L148)

- [LiquidStakingManager.sol#L392](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L392)
- [LiquidStakingManager.sol#L465](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L465)
- [LiquidStakingManager.sol#L538](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L538)
- [LiquidStakingManager.sol#L587](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L587)

- [SavETHVault.sol#L63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L63)
- [SavETHVault.sol#L103](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L103)
- [SavETHVault.sol#L116](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L116)

- [StakingFundsVault.sol#L78](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L78)
- [StakingFundsVault.sol#L152](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L152)
- [StakingFundsVault.sol#L166](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L166)
- [StakingFundsVault.sol#L203](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L203)
- [StakingFundsVault.sol#L266](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L266)
- [StakingFundsVault.sol#L276](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L276)
- [StakingFundsVault.sol#L286](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L286)


## 10. require()/revert() strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

- [ETHPoolLPFactory.sol#L122](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L122)
- [ETHPoolLPFactory.sol#L130](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L130)

- [GiantPoolBase.sol#L55](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L55)

- [GiantSavETHVaultPool.sol#L151](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L151)

- [LiquidStakingManager.sol#L256](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L256)
- [LiquidStakingManager.sol#L257](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L257)
- [LiquidStakingManager.sol#L258](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L258)
- [LiquidStakingManager.sol#L268](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L268)
- [LiquidStakingManager.sol#L280](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L280)
- [LiquidStakingManager.sol#L291](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L291)
- [LiquidStakingManager.sol#L328](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L328)
- [LiquidStakingManager.sol#L331](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L331)
- [LiquidStakingManager.sol#L332](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L332)
- [LiquidStakingManager.sol#L393](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L393)
- [LiquidStakingManager.sol#L396](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L396)
- [LiquidStakingManager.sol#L435](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L435)
- [LiquidStakingManager.sol#L437](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L437)
- [LiquidStakingManager.sol#L455](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L455)
- [LiquidStakingManager.sol#L469](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L469)
- [LiquidStakingManager.sol#L541](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L541)
- [LiquidStakingManager.sol#L589](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L589)
- [LiquidStakingManager.sol#L662](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L662)
- [LiquidStakingManager.sol#L663](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L663)
- [LiquidStakingManager.sol#L936](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L936)
- [LiquidStakingManager.sol#L939](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L939)
- [LiquidStakingManager.sol#L940](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L940)
- [LiquidStakingManager.sol#L943](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L943)
- [LiquidStakingManager.sol#L944](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L944)

- [SavETHVault.sol#L64](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L64)
- [SavETHVault.sol#L84](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L84)
- [SavETHVault.sol#L90](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L90)
- [SavETHVault.sol#L204](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L204)

- [StakingFundsVault.sol#L79](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79)
- [StakingFundsVault.sol#L114](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114)
- [StakingFundsVault.sol#L120](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L120)
- [StakingFundsVault.sol#L154](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L154)
- [StakingFundsVault.sol#L240](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L240)
- [StakingFundsVault.sol#L267](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L267)

- [OwnableSmartWallet.sol#L33](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L33)
- [OwnableSmartWallet.sol#L100](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L100)
- [OwnableSmartWallet.sol#L115](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L115)


## 11. splitting require() statements that use `&&` saves gas

this will have a large deployment gas cost but with enough runtime calls the split require version will be 3 gas cheaper

- [LiquidStakingManager.sol#L357](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L357)


## 12. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. 

- [StakingFundsVault.sol#L355](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L355)

- [OwnableSmartWallet.sol#L41](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L41)
- [OwnableSmartWallet.sol#L52](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L52)
- [OwnableSmartWallet.sol#L67](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L67)

- [Syndicate.sol#L129](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L129)
- [Syndicate.sol#L337](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L337)
- [Syndicate.sol#L343](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L343)

## 13. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

`_onWithdraw`(its inherited but still used once in the whole project)
- [GiantPoolBase.sol#L103](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L103)

`_assertUserHasEnoughGiantLPToClaimVaultLP`
- [GiantPoolBase.sol#L93](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L93)

`_onDepositETH`(its inherited but still used once in the whole project)
- [GiantPoolBase.sol#L100](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L100)

`_isNodeRunnerValid`
- [LiquidStakingManager.sol#L684](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L684)

`_stake`
- [LiquidStakingManager.sol#L739](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L739)

`_joinLSDNStakehouse`
- [LiquidStakingManager.sol#L776](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L776)

`_createLSDNStakehouse`
- [LiquidStakingManager.sol#L816](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L816)

`_initSavETHVault`
- [LiquidStakingManager.sol#L904](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L904)

`_initStakingFundsVault`
- [LiquidStakingManager.sol#L911](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L911)

`_calculateCommission`
- [LiquidStakingManager.sol#L921](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L921)

`_assertEtherIsReadyForValidatorStaking`
- [LiquidStakingManager.sol#L934](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L934)


## 14. internal or private functions not called by the contract or inherited ones should be removed to save deployment gas

`_beforeTokenTransfer`
- [GiantLP.sol#L39](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L39)
- [LPToken.sol#L61](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L61)

`_afterTokenTransfer`
- [GiantLP.sol#L43](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L43)
- [LPToken.sol#L66](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L66)


## 15. bytes constants are more efficient than string constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

- [LiquidStakingManager.sol#L111](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L111)
- [LiquidStakingManager.sol#L255](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L255)
- [LiquidStakingManager.sol#L656](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L656)


## 16. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

`totalRewardsReceived`
- [GiantMevAndFeesPool.sol#L176](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L176)

`isKnotDeregistered`
- [LiquidStakingManager.sol#L514](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L514)

`deployNewLiquidStakingDerivativeNetwork`
- [LSDNFactory.sol#L73](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L73)

`withdrawETHForStaking`
- [SavETHVault.sol#L200](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L200)

`withdrawETH`
- [StakingFundsVault.sol#L239](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L239)


## 17. should use arguments instead of state variable

This will save near 97 gas

- [LiquidStakingManager.sol#L270](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L270)
- [LiquidStakingManager.sol#L272](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L272)


## 18. Don’t compare boolean expressions to boolean literals

`if (<x> == true) => if (<x>)`
`if (<x> == false) => if (!<x>)`
`require(<x> == true) => require(<x>)`
`require(<x> == false) => require(!<x>)`

- [LiquidStakingManager.sol#L436](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L436)
- [LiquidStakingManager.sol#L437](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L437)
- [LiquidStakingManager.sol#L688](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L688)

- [GiantSavETHVaultPool.sol#L150](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L150)

- [LiquidStakingManager.sol#L291](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L291)
- [LiquidStakingManager.sol#L328](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L328)
- [LiquidStakingManager.sol#L332](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L332)
- [LiquidStakingManager.sol#L393](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L393)
- [LiquidStakingManager.sol#L469](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L469)
- [LiquidStakingManager.sol#L541](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L541)
- [LiquidStakingManager.sol#L589](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L589)

- [SavETHVault.sol#L64](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L64)
- [SavETHVault.sol#L84](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L84)

- [StakingFundsVault.sol#L79](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79)
- [StakingFundsVault.sol#L114](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114)
- [StakingFundsVault.sol#L205](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L205)

- [Syndicate.sol#L611](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L611)
- [Syndicate.sol#L612](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L612)

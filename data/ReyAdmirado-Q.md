
| | issue |
| ----------- | ----------- |
| 1 | [typo](#1-typo) |
| 2 | [use of floating pragma](#2-use-of-floating-pragma) |
| 3 | [event is missing indexed fields](#3-event-is-missing-indexed-fields) |
| 4 | [_safemint() should be used rather than _mint() wherever possible](#4-_safemint-should-be-used-rather-than-_mint-wherever-possible) |
| 5 | [constants should be defined rather than using magic numbers](#5-constants-should-be-defined-rather-than-using-magic-numbers) |
| 6 | [require check will always be false!](#6-require-check-will-always-be-false) |
| 7 | [state var should be constant](#7-state-var-should-be-constant) |
| 8 | [lines are too long](#8-lines-are-too-long) |
| 9 | [open todos](#9-open-todos) |
| 10 | [Outdated compiler version](#10-outdated-compiler-version) |
| 11 | [incorrect functions visibility](#11-incorrect-functions-visibility) |
| 12 | [2**<n> - 1 should be re-written as type(uint<n>).max](#12-2---1-should-be-re-written-as-typeuintmax) |
| 13 | [Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)](#13-use-a-solidity-version-of-at-least-0812-to-get-stringconcat-to-be-used-instead-of-abiencodepacked) |
| 14 | [Unused/empty receive()/fallback() function](#14-unusedempty-receivefallback-function) |
| 15 | [Use Underscores for Number Literals](#15-use-underscores-for-number-literals) |
| 16 | [Constant redefined elsewhere](#16-constant-redefined-elsewhere) |
| 17 | [Duplicated require()/revert() checks should be refactored to a modifier or function](#17-duplicated-requirerevert-checks-should-be-refactored-to-a-modifier-or-function) |
| 18 | [abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()](#18-abiencodepacked-should-not-be-used-with-dynamic-types-when-passing-the-result-to-a-hash-function-such-as-keccak256) |

## 1. typo 

Instane --> Instance
- [ETHPoolLPFactory.sol#L74](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L74)

depoistor --> depositor
- [ETHPoolLPFactory.sol#L124](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L124)
- [ETHPoolLPFactory.sol#L150](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L150)

trigerringAddress --> triggeringAddress
- [LiquidStakingManager.sol#L51](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L51)

admiting --> admitting
- [LiquidStakingManager.sol#L101](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L101)

Unrecognised --> Unrecognized
- [LiquidStakingManager.sol#L436](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L436)

validtor --> validator
- [LiquidStakingManager.sol#L476](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L476)

initals --> initials
- [LiquidStakingManager.sol#L476](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L476)

overriden --> overridden

- [LiquidStakingManager.sol#L903](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L903)
- [LiquidStakingManager.sol#L920](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L920)

customise --> customize
- [LiquidStakingManager.sol#L920](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L920)

Inconsisent --> Inconsistent
- [SavETHVault.sol#L115](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L115)

determins --> determines
- [SavETHVault.sol#L227](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L227)

publice --> public
- [Syndicate.sol#L356](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L356)

collatearlized --> collateralized
- [Syndicate.sol#L398](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L398)

amongs --> amongst
- [Syndicate.sol#L490](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L490)


## 2. use of floating pragma
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

- [ETHPoolLPFactory.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L)

- [GiantLP.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L)

- [GiantMevAndFeesPool.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L)

- [GiantPoolBase.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L)

- [GiantSavETHVaultPool.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L)

- [LiquidStakingManager.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L)

- [OptionalGatekeeperFactory.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L)

- [OptionalHouseGatekeeper.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L)

- [StakingFundsVaultDeployer.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L)

- [LPToken.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L)

- [LPTokenFactory.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L)

- [LSDNFactory.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L)

- [SavETHVault.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L)

- [StakingFundsVault.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L)

- [SyndicateRewardsProcessor.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L)

- [OwnableSmartWallet.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L)

- [OwnableSmartWalletFactory.sol#L](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L)

## 3. event is missing indexed fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

- [LiquidStakingManager.sol#L66](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L66)

- [ETHPoolLPFactory.sol#L19](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L19)
- [ETHPoolLPFactory.sol#L22](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L22)
- [ETHPoolLPFactory.sol#L25](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L25)

- [GiantPoolBase.sol#L19](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L19)

- [GiantSavETHVaultPool.sol#L16](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L16)

- [SavETHVault.sol#L22](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L22)

- [Syndicate.sol#L63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L63)


## 4. _safemint() should be used rather than _mint() wherever possible

- [GiantLP.sol#L41](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L41)

- [LPToken.sol#L47](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L47)


## 5. constants should be defined rather than using magic numbers 
Even assembly can benefit from using readable constants instead of hex/numeric literals

- [LiquidStakingManager.sol#L256](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L256)
- [LiquidStakingManager.sol#L257](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L257)
- [LiquidStakingManager.sol#L870](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L870)

- [ETHPoolLPFactory.sol#L88](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L88)
- [ETHPoolLPFactory.sol#L89](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L89)
- [ETHPoolLPFactory.sol#L112](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L112)


## 6. require check will always be false!!

the require check will always be false so it the function will not do what it was designed to do and it will always revert `Unnecessary update to same status` 

- [LiquidStakingManager.sol#L280](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L280)


## 7. state var should be constant 

`MODULO`
- [LiquidStakingManager.sol#L158](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158)


## 8. lines are too long
Usually lines in source code are limited to 80 characters. Its advised to keep lines lower than 120 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

- [LiquidStakingManager.sol#L222](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L222)

- [SavETHVault.sol#L222](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L222)

- [Syndicate.sol#L116](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L116)
- [Syndicate.sol#L119](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L119)


## 9. open todos
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

- [Syndicate.sol#L195](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L195)

## 10. Outdated compiler version
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs


## 11. incorrect functions visibility
Whenever a function is not being called internally in the code, it can be easily declared as external. While the entire code base have explicit visibilities for every function, some of them can be changed to be external.

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


## 12. 2**<n> - 1 should be re-written as type(uint<n>).max
Earlier versions of solidity can use uint<n>(-1) instead. Expressions not including the - 1 can often be re-written to accommodate the change (e.g. by using a > rather than a >=, which will also save some gas)

- [LiquidStakingManager.sol#L870](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L870)


## 13. Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)

- [ETHPoolLPFactory.sol#L135](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L135)
- [ETHPoolLPFactory.sol#L136](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L136)


## 14. Unused/empty receive()/fallback() function
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

- [LiquidStakingManager.sol#L629](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L629)

- [SyndicateRewardsProcessor.sol#L98](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98)

- [OwnableSmartWallet.sol#L148](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L148)

- [Syndicate.sol#L352](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L352)


## 15. Use Underscores for Number Literals
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

starting from right put underscores every 3 numbers
- [LiquidStakingManager.sol#L158](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158)



## 16. Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.
https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678

- [ETHPoolLPFactory.sol#L38](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L38)

- [GiantPoolBase.sol#L29](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L29)

- [SyndicateRewardsProcessor.sol#L15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L15)

- [Syndicate.sol#L66](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L66)


## 17. Duplicated require()/revert() checks should be refactored to a modifier or function

many require()s are being used twice but some are used over 3 times: 

require(smartWallet != address(0), "No smart wallet"); // used 4 times
- [LiquidStakingManager.sol#L294](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L294)


## 18. abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode instead 

- [ETHPoolLPFactory.sol#L135](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L135)
- [ETHPoolLPFactory.sol#L136](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L136)

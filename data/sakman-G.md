## 1. Use bitshifting instead of multiplication and divison

_contracts/syndicate/Syndicate.sol:_ [L546](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L546)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L434](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L434)

## 2. Consider marking functions as `payable` if there is no risk of sending value through them
### This change will save gas each time a function is called

_contracts/syndicate/Syndicate.sol:_ [L154](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L154)
[L161](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L161)
[L168](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L168)

_contracts/liquid-staking/GiantLP.sol:_ [L29](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L29)
[L34](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L34)

_contracts/liquid-staking/GiantMevAndFeesPool.sol:_ [L146](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L146)
[L170](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L170)

_contracts/smart-wallet/OwnableSmartWallet.sol:_ [L110](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L110)

_contracts/liquid-staking/StakingFundsVault.sol:_ [L56](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L56)
[L239](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L239)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L218](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L218)
[L249](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L249)
[L255](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L255)
[L267](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L267)
[L308](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L308)

## 3. `public` storage `constant`(and `immutable`) variable should be `private`
### saves tons of gas on deployment

_contracts/liquid-staking/SyndicateRewardsProcessor.sol:_ [L15](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L15)

_contracts/liquid-staking/ETHPoolLPFactory.sol:_ [L38](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L38)

_contracts/syndicate/Syndicate.sol:_ [L66](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L66)

_contracts/liquid-staking/GiantPoolBase.sol:_ [L22](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L22)

## 4. Prefix incrementing and decrementing costs around 6 gas less than the postfix ones
### e.g. ++var is cheaper than var++

_contracts/liquid-staking/ETHPoolLPFactory.sol:_ [L144](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L144)

## 5. Use `constant` and `immutable` for constants

_contracts/liquid-staking/StakingFundsVaultDeployer.sol:_ [L13](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L13)

_contracts/liquid-staking/ETHPoolLPFactory.sol:_ [L28](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L28)
[L29](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L29)
[L41](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L41)

_contracts/liquid-staking/SyndicateRewardsProcessor.sol:_ [L18](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L18)

_contracts/liquid-staking/GiantLP.sol:_ [L11](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L11)
[L14](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L14)

_contracts/liquid-staking/LSDNFactory.sol:_ [L15](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L15)
[L21](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L21)
[L24](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L24)
[L27](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L27)
[L30](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L30)
[L36](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L36)

_contracts/liquid-staking/SavETHVaultDeployer.sol:_ [L13](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L13)

_contracts/liquid-staking/OptionalHouseGatekeeper.sol:_ [L12](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L12)

_contracts/syndicate/SyndicateFactory.sol:_ [L13](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol#L13)

_contracts/liquid-staking/GiantPoolBase.sol:_ [L28](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L28)
[L31](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L31)

_contracts/liquid-staking/LPTokenFactory.sol:_ [L15](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPTokenFactory.sol#L15)

_contracts/liquid-staking/LPToken.sol:_ [L14](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol#L14)
[L17](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol#L17)

## 6. Strings messages in `require` statements should not be longer than 32 bytes of length

_contracts/liquid-staking/StakingFundsVault.sol:_ [L114](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L114)
[L120](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L120)
[L154](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L154)
[L240](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L240)
[L267](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L267)

_contracts/liquid-staking/SavETHVault.sol:_ [L64](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L64)
[L90](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L90)
[L204](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L204)

_contracts/liquid-staking/ETHPoolLPFactory.sol:_ [L122](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L122)
[L130](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L130)

_contracts/smart-wallet/OwnableSmartWallet.sol:_ [L33-L36](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L33-L36)
[L96-L99](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L96-L99)
[L111-L114](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L111-L114)

_contracts/liquid-staking/GiantPoolBase.sol:_ [L55](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L55)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L256](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L256)
[L257](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L257)
[L268](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L268)
[L280](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L280)
[L291](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L291)
[L328](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L328)
[L332](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L332)
[L393](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L393)
[L396](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L396)
[L437](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L437)
[L469](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L469)
[L662](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L662)
[L663](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L663)
[L936](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L936)
[L940](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L940)
[L944](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L944)

_contracts/liquid-staking/GiantSavETHVaultPool.sol:_ [L149-L152](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L149-L152)

## 7. Place `i++` in an `unchecked` blocks in for-loops

_contracts/liquid-staking/GiantPoolBase.sol:_ [L76](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L76)

_contracts/liquid-staking/GiantSavETHVaultPool.sol:_ [L42](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L42)
[L78](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L78)
[L128](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L128)
[L146](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L146)
[L148](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L148)

_contracts/liquid-staking/ETHPoolLPFactory.sol:_ [L63](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L63)

_contracts/liquid-staking/StakingFundsVault.sol:_ [L78](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L78)
[L203](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L203)
[L266](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L266)
[L276](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L276)
[L286](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L286)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L392](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L392)
[L538](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L538)
[L587](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L587)

_contracts/syndicate/Syndicate.sol:_ [L211](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L211)
[L259](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L259)
[L301](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L301)
[L346](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L346)
[L420](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L420)
[L585](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L585)
[L598](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L598)

_contracts/liquid-staking/SavETHVault.sol:_ [L63](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L63)
[L103](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L103)
[L116](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L116)

_contracts/liquid-staking/GiantMevAndFeesPool.sol:_ [L38](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L38)
[L64](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L64)
[L90](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L90)
[L117](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L117)
[L135](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L135)

## 8. When comparing variables of type `uint`, use `require(x != 0)` instead of `require(x > 0)`

_contracts/liquid-staking/ETHPoolLPFactory.sol:_ [L59](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L59)

_contracts/liquid-staking/SyndicateRewardsProcessor.sol:_ [L59](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L59)
[L62](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L62)
[L77](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L77)
[L81](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L81)

_contracts/liquid-staking/GiantMevAndFeesPool.sol:_ [L62](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L62)
[L112](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L112)
[L132](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L132)

_contracts/liquid-staking/StakingFundsVault.sol:_ [L71](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L71)
[L150](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L150)
[L164](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L164)

_contracts/liquid-staking/GiantPoolBase.sol:_ [L71](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L71)

_contracts/liquid-staking/GiantSavETHVaultPool.sol:_ [L72](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L72)
[L123](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L123)
[L143](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L143)

_contracts/syndicate/Syndicate.sol:_ [L315](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L315)
[L500](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L500)
[L588](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L588)

_contracts/liquid-staking/SavETHVault.sol:_ [L59](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L59)
[L101](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L101)
[L114](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L114)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L414](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L414)
[L532](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L532)
[L583](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L583)

## 9. You can make state variables take less slots than they currently are

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L90](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L90)

## 10. Use 1 and 2 for true and false

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L120](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L120)

## 11. Use `require(x)` instead of `require(x == true)`

_contracts/syndicate/Syndicate.sol:_ [L611](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L611)
[L612](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L612)

_contracts/liquid-staking/SavETHVault.sol:_ [L64](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L64)
[L84](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L84)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L291](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L291)
[L328](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L328)
[L332](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L332)
[L436](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L436)
[L469](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L469)
[L541](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L541)
[L589](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L589)

_contracts/liquid-staking/GiantSavETHVaultPool.sol:_ [L150](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L150)

_contracts/liquid-staking/StakingFundsVault.sol:_ [L79](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L79)
[L114](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L114)
[L205](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L205)

## 12. Calldata is cheaper than memory for function input

_contracts/syndicate/Syndicate.sol:_ [L132](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L132)
[L337](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L337)
[L343](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L343)
[L359](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L359)
[L472](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L472)
[L473](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L473)
[L491](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L491)
[L555](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L555)
[L631](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L631)

_contracts/smart-wallet/OwnableSmartWallet.sol:_ [L41](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L41)
[L54](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L54)
[L69](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L69)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L879](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L879)

_contracts/liquid-staking/StakingFundsVault.sol:_ [L355](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L355)
[L360](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L360)

## 13. Use multiple `require`s instead of a single one with `&&`

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L357](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L357)

## 14. Use `x < y + 1` in stead of `x <= y`

_contracts/liquid-staking/StakingFundsVault.sol:_ [L176](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L176)
[L240](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L240)
[L241](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L241)

_contracts/liquid-staking/GiantMevAndFeesPool.sol:_ [L116](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L116)

_contracts/liquid-staking/GiantSavETHVaultPool.sol:_ [L92](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L92)
[L127](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L127)

_contracts/liquid-staking/LiquidStakingManager.sol:_ [L256](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L256)
[L257](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L257)
[L333](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L333)
[L432](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L432)
[L662](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L662)
[L936](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L936)

_contracts/liquid-staking/GiantPoolBase.sol:_ [L35](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L35)
[L53](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L53)
[L55](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L55)
[L94](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L94)
[L95](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L95)

_contracts/liquid-staking/ETHPoolLPFactory.sol:_ [L80](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L80)
[L83](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L83)
[L111](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L111)
[L122](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L122)
[L130](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L130)

_contracts/liquid-staking/SavETHVault.sol:_ [L127](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L127)
[L128](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L128)
[L204](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L204)
[L205](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L205)


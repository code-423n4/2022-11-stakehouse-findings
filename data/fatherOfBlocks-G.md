**contracts/liquid-staking/LPTokenFactory.sol**
- L19/33/34/35 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L15 - lpTokenImplementation is only set in the constructor and does not have a setter in the contract, therefore it could be immutable and generate less gas costs.


**contracts/liquid-staking/GiantLP.sol**
- L30/35 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L11/14/25/26 - pool and transferHookProcessor are only set in the constructor and do not have their setters in the contract, therefore they could be immutable and generate less gas costs.


**contracts/syndicate/SyndicateFactory.sol**
- L13/17 - syndicateImplementation is only set in the constructor and does not have a setter in the contract, therefore it could be immutable and generate less gas costs.


**contracts/liquid-staking/GiantPoolBase.sol**
- L35/36/53/54/55/61/71/72/94/95/96 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L55 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L71 - It is less expensive to validate that uint != 0 than to validate uint > 0


**contracts/liquid-staking/LSDNFactory.sol**
- L51-58 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**contracts/liquid-staking/GiantSavETHVaultPool.sol**
- L36/37/38/39/49/72/73/74/89/92/123/124/125/126/127/143/144/145/149 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L36/72/123/143 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L151 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L150 - When we want to validate conditions that are bools instead of validating (condition != true or condition == false) it is less expensive to validate (condition or !condition)


**contracts/smart-wallet/OwnableSmartWallet.sol**
- L33/79/100/115 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L35/102/117 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/liquid-staking/SavETHVault.sol**
- L50/59/60/64/65/76/84/85/90/101/102/114/115/127/128/134/161/186/190/204/205/206/207/210/236/237 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L64/84/90/204 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L59/101/114 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L64/84 - When we want to validate conditions that are bools instead of validating (condition != true or condition == false) it is less expensive to validate (condition or !condition)


**contracts/liquid-staking/GiantMevAndFeesPool.sol**
- L35/62/112/132 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L35/36/37/43/62/63/87/112/113/114/115/116/132/133/134/147/171 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L87/90/183 - When the length of an array is used more than once, it is less expensive to create a variable in length memory and use that variable.


**contracts/liquid-staking/StakingFundsVault.sol**
- L51/71/72/79/80/107/114/115/120/150/151/154/164/165/175/176/177/180/191/204/210/229/230/240/241/242/245/267/320/346/361/372/373 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L79/114/120/154/240/267 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L79/114/205 - When we want to validate conditions that are bools instead of validating (condition != true or condition == false) it is less expensive to validate (condition or !condition)

- L71/150/164/320/346 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L203/266/276/286 - When the length of an array is used more than once, it is less expensive to create a variable in length memory and use that variable.


**contracts/syndicate/Syndicate.sol**
- L177/183/315/500/551/588/634/656 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L204/205/211/251/252/259/294/301/346/584/585/598/641/648/ - When the length of an array is used more than once, it is less expensive to create a variable in length memory and use that variable.

- L431 - The operation currentAccrued += balance * unprocessedForKnot / (4 ether - currentSlashedAmount); It could be uncheckd since in L429 it is validated that the division will never be by 0, therefore it will always give a result >= 0.

- L611/612 - When we want to validate conditions that are bools instead of validating (condition != true or condition == false) it is less expensive to validate (condition or !condition)


**contracts/liquid-staking/SyndicateRewardsProcessor.sol**
- L37/59/62/77/81 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L57/68 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**contracts/liquid-staking/ETHPoolLPFactory.sol**
- L59/60/61/77/78/79/80/81/82/83/88/89/91/96/111/112/122/130 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L59 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L122/130 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/liquid-staking/LiquidStakingManager.sol**
- L161/209/240/241/250/256/257/258/268/279/280/290/291/294/295/296/309/312/313/314/327/328/331/332/333/334/357/360/361/364/386/387/390/393/396/412/416/432-437/455/461/469/471/532-536/541/544/545/583-585/589/592/658-663/658/688/922/936/939/940/943/944/949 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L256/257/258/268/280/291/328/331/332/393/396/435/437/455/469/541/589/662/663/936/939/940/944 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L291/328/332/393/436/437/469/541/589/688 - When we want to validate conditions that are bools instead of validating (condition != true or condition == false) it is less expensive to validate (condition or !condition)

- L386/414/532/583/924 - It is less expensive to validate that uint != 0 than to validate uint > 0

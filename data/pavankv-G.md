1. use Call data instead of memory to save gas in external function.

If memory is used each data is to copy by running loop again by call data so by using call data it bypass .

code snippet:-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L132
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L133
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L472
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L555
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L355



2. cache the array length to save gas-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L585
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L211
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L259
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L301
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L346
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L648
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L392



3. use x=x+y and x=x-y instead of += , -= to save gas:-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L317
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L269
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L273
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L185
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L190
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L225
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L226
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L227
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L621
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L623
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L658
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L782
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L770
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L782
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L839
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L97
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L71
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L39
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L57


4.Expressions that cannot be overflowed and underflow can be unchecked :-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L346
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L409
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L551
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L522
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L431
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L339
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L389
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L925

5. Caching storage values in memory:-

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L228
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L366
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L393
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L546
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L551
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L665
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L185
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L228.

varaibles list  add this to memory because calling storage is expensive than memory:-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L69
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L72
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L75
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L78
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L81
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L84
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L87
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L155
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L40





6. Use private for constant instead of public:-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L66



7. unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops .:-

code snippet:-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L585
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L211
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L259
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L301
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L346
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L648
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L269
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L272
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L273
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L392
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L465
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L538
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L587
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L152
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L166
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L203
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L266
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L276
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L286
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L63
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L103
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L116
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L38
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L64
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L90
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L117
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L135
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L183
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L63
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L103
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L116
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L42
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L78
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L82
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L128
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L146
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L148
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L76

8. name varaible in returns to save gas:-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L378
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L267
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L274
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L284
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L126

9. use custom error insted of strings in require to save gas and reduce string size to 32 bytes save gas:-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L209
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L240
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L241
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L250
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L256
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L257
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L258
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L279
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L280
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L290
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L291
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L294
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L296
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L295
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L309
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L312
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L313
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L314
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L327
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L328
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L331
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L332
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L334
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L357
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L360
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L361
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L364
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L386
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L387
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L390
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L393
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L396
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L412
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L416
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L432
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L433
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L434
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L435
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L436
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L437
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L461
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L469
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L532
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L533
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L534
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L535
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L536
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L541
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L544
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L545
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L583
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L584
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L585
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L589
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L592
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L658
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L659
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L660
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L661
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L662
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L663
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L685
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L688
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L734
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L922
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L936
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L939
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L940
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L943
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L944
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L949
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L51
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L71
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L72
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L80
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L107
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L115
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L120
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L150
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L151
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L154
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L164
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L165
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L175
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L176
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L177
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L180
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L184
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L191
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L204
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L210
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L229
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L230
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L240
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L241
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L242
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L245
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L267
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L320
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L346
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L361
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L372
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L373
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L50
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L59
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L60
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L64
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L64
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L76
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L84
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L85
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L90
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L101
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L102
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L114
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L115
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L127
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L128
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L134
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L161
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L186
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L190
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L204
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L205
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L206
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L207
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L210
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L236
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L237
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L35
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L36
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L37
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L43
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L62
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L63
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L87
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L112
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L113
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L114
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L115
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L116
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L132
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L133
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L134
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L171
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L59
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L60
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L64
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L65
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L76
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L84
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L85
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L90
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L101
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L114
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L127
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L128
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L134
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L161
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L186
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L190
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L204
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L205
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L206
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L207
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L236
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L237
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L33
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L100
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L115
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L79
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L36
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L37
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L38
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L39
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L49
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L72
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L73
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L74
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L89
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L92
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L123
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L124
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L125
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L126
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L127
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L143
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L144
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L145
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L149
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L51
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L52
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L53
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L54
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L55
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L56
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L57
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L58
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L35
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L36
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L53
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L54
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L55
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L61
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L71
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L72
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L94
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L95
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L96
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L30
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L37

10. storage pointer to a structure is cheaper than copying each value of the structure into memory, same for array and mapping:-

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L805
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L859
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L860
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L893
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L896
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L179
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L295
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L319
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L324
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L131
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L229

11. use != instead of >  in require statements to save gas :p-
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L62 
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L132
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L59
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L101
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L114
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L36
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L72
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L123
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L143
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L71


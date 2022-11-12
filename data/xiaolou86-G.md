1. Use unchecked for arithmetic in the for loop where it is unsigned and won't overflow.

File: liquid-staking/LiquidStakingManager.sol:
392:        for(uint256 i; i < _blsPubKeys.length; ++i) {

File: liquid-staking/LiquidStakingManager.sol:
465:        for(uint256 i; i < len; ++i) {

File: liquid-staking/LiquidStakingManager.sol:
538:        for (uint256 i; i < numOfValidators; ++i) {

File: liquid-staking/LiquidStakingManager.sol:
587:        for (uint256 i; i < numOfKnotsToProcess; ++i) {

File: liquid-staking/GiantPoolBase.sol:
76:        for (uint256 i; i < amountOfTokens; ++i) {

File: liquid-staking/GiantSavETHVaultPool.sol:
42:        for (uint256 i; i < numOfSavETHVaults; ++i) {

File: liquid-staking/GiantSavETHVaultPool.sol:
78:        for (uint256 i; i < numOfVaults; ++i) {

File: liquid-staking/GiantSavETHVaultPool.sol:
82:            for (uint256 j; j < _lpTokens[i].length; ++j) {

File: liquid-staking/GiantSavETHVaultPool.sol:
128:        for (uint256 i; i < numOfRotations; ++i) {

File: liquid-staking/GiantSavETHVaultPool.sol:
146:        for (uint256 i; i < numOfVaults; ++i) {

File: liquid-staking/GiantSavETHVaultPool.sol:
148:            for (uint256 j; j < _lpTokens[i].length; ++j) {

File: liquid-staking/GiantMevAndFeesPool.sol:
38:        for (uint256 i; i < numOfVaults; ++i) {

File: liquid-staking/GiantMevAndFeesPool.sol:
64:        for (uint256 i; i < numOfVaults; ++i) {

File: liquid-staking/GiantMevAndFeesPool.sol:
90:        for (uint256 i; i < _stakingFundsVaults.length; ++i) {

File: liquid-staking/GiantMevAndFeesPool.sol:
117:        for (uint256 i; i < numOfRotations; ++i) {

File: liquid-staking/GiantMevAndFeesPool.sol:
135:        for (uint256 i; i < numOfVaults; ++i) {

File: liquid-staking/GiantMevAndFeesPool.sol:
183:        for (uint256 i; i < _lpTokens.length; ++i) {

File: liquid-staking/SavETHVault.sol:
63:        for (uint256 i; i < numOfValidators; ++i) {

File: liquid-staking/SavETHVault.sol:
103:        for (uint256 i; i < numOfTokens; ++i) {

File: liquid-staking/SavETHVault.sol:
116:        for (uint256 i; i < numOfTokens; ++i) {

File: liquid-staking/StakingFundsVault.sol:
78:        for (uint256 i; i < numOfValidators; ++i) {

File: liquid-staking/StakingFundsVault.sol:
152:        for (uint256 i; i < numOfTokens; ++i) {

File: liquid-staking/StakingFundsVault.sol:
166:        for (uint256 i; i < numOfTokens; ++i) {

File: liquid-staking/StakingFundsVault.sol:
203:        for (uint256 i; i < _blsPubKeys.length; ++i) {

File: liquid-staking/StakingFundsVault.sol:
266:        for (uint256 i; i < _blsPublicKeys.length; ++i) {

File: liquid-staking/StakingFundsVault.sol:
276:        for (uint256 i; i < _blsPubKeys.length; ++i) {

File: liquid-staking/StakingFundsVault.sol:
286:        for (uint256 i; i < _token.length; ++i) {

File: liquid-staking/ETHPoolLPFactory.sol:
63:        for (uint256 i; i < numOfRotations; ++i) {

File: liquid-staking/ETHPoolLPFactory.sol:
144:            numberOfLPTokensIssued++;

File: syndicate/Syndicate.sol:
211:        for (uint256 i; i < _blsPubKeys.length; ++i) {

File: syndicate/Syndicate.sol:
259:        for (uint256 i; i < _blsPubKeys.length; ++i) {

File: syndicate/Syndicate.sol:
301:        for (uint256 i; i < _blsPubKeys.length; ++i) {

File: syndicate/Syndicate.sol:
346:        for (uint256 i; i < _blsPubKeys.length; ++i) {

File: syndicate/Syndicate.sol:
420:        for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

File: syndicate/Syndicate.sol:
513:                    for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

File: syndicate/Syndicate.sol:
560:        for (uint256 i; i < knotsToRegister; ++i) {

File: syndicate/Syndicate.sol:
585:        for (uint256 i; i < _priorityStakers.length; ++i) {

File: syndicate/Syndicate.sol:
598:        for (uint256 i; i < _blsPublicKeys.length; ++i) {

File: syndicate/Syndicate.sol:
648:        for (uint256 i; i < _blsPubKeys.length; ++i) {

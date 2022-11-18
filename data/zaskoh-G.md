# update pragma versioning to 0.8.17
Next to some some bugs fixed in this version you also save on gas. How to update to 0.8.17 is mentionioned in QA.

```bash
➜  2022-11-stakehouse git:(main) ✗ forge snapshot --diff
[⠢] Compiling...
[⠘] Compiling 100 files with 0.8.17
[⠢] Solc 0.8.17 finished in 12.03s
Compiler run successful
....
testOnlySavETHVaultCanBurn() (gas: 0 (0.000%))
testOnlySavETHVaultCanMint() (gas: 0 (0.000%))
testSetupIsCorrect() (gas: 0 (0.000%))
testTokenDeploymentRevertsWhenNameIsEmpty() (gas: 0 (0.000%))
testTokenDeploymentRevertsWhenSavETHVaultIsZero() (gas: 0 (0.000%))
testTokenDeploymentRevertsWhenSymbolIsEmpty() (gas: 0 (0.000%))
testBurnLPRevertsWhenAmountIsZero() (gas: 0 (0.000%))
testSetupWasSuccessful() (gas: 0 (0.000%))
testWithdrawETHForStakingRevertsWhenAmountIsZero() (gas: 0 (0.000%))
testWithdrawETHForStakingRevertsWhenNotManager() (gas: 0 (0.000%))
testBatchDepositETHForStakingRevertsWhenEmptyArraysAreSupplied() (gas: 0 (0.000%))
testBurnLPTokensByBLSRevertsWhenArraysAreEmpty() (gas: 0 (0.000%))
testBurnLPTokensRevertsWhenArrayIsEmpty() (gas: 0 (0.000%))
testBurnLPTokensRevertsWhenArrayLengthsAreInconsistent() (gas: 0 (0.000%))
testCanReceiveETHAndWithdraw() (gas: 0 (0.000%))
testSupply() (gas: 0 (0.000%))
testBatchDepositETHForStakingRevertsWhenBLSNotLifecycleStatusOne() (gas: 3 (0.005%))
testDepositETHForStaking() (gas: -82 (-0.007%))
testMint() (gas: 10 (0.009%))
testBatchDepositETHForStakingRevertsWhenBLSNotRegisteredWithNetwork() (gas: -5 (-0.010%))
testSecondDepositForKnotMintsSameLP() (gas: -75 (-0.012%))
testDepositETHForStakingRevertsWhenAmountDoesNotMatchValue() (gas: 12 (0.012%))
testDepositETHForStakingRevertsWhenAmountIsZero() (gas: 12 (0.014%))
testDepositETHForStakingRevertsWhenBLSKeyIsBanned() (gas: 8 (0.014%))
testBurn() (gas: 14 (0.016%))
testBatchDepositETHForStakingRevertsWhenETHNotAttached() (gas: -259 (-0.019%))
testDepositETHForStakingRevertsWhenInitialsNotRegisteredForBLSKey() (gas: 12 (0.019%))
testBurnLPRevertsWhenBalanceIsZero() (gas: -118 (-0.022%))
testBurnLPForETHWorksAsExpected() (gas: -120 (-0.022%))
testDepositETHForStakingRevertsWhenLifecycleIsNotInitialsRegistered() (gas: 12 (0.022%))
testBatchDepositETHForStakingCanSuccessfullyDepositForMultipleValidators() (gas: -337 (-0.024%))
testDepositETHForStakingRevertsWhenBLSKeyIsNotRegistered() (gas: 8 (0.024%))
testBurnLPRevertsWhenETHIsStaked() (gas: -147 (-0.027%))
testDepositETHForStakingRevertsWhenNoETHAttached() (gas: 12 (0.029%))
testBurnLPBeforeETHIsStaked() (gas: -162 (-0.030%))
testBurnLPTokensWorksAsExpectedForMultipleValidators() (gas: -433 (-0.031%))
testFirstDepositsForKnotAreBetweenOneAndTwentyFour(uint256) (gas: -172 (-0.031%))
testFirstDepositDeploysAnLPToken() (gas: -172 (-0.031%))
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormationAndBalIncrease() (gas: -282 (-0.043%))
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormation() (gas: -282 (-0.045%))
testBothCollateralizedAndSlotClaim() (gas: -599 (-0.110%))
testBurnLPTokensByBLSRevertsWhenNothingStakedForBLS() (gas: -25 (-0.113%))
testReceiveETHAndDistributeDuringLPTransfers() (gas: -3671 (-0.114%))
testReceiveETHAndDistributeToMultipleLPs() (gas: -3633 (-0.114%))
testClaimAsCollateralizedSlotOwner() (gas: -301 (-0.115%))
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -3720 (-0.119%))
testThatAfterFirstKnotSecondKnotOnwardsJoinsTheLSDHouse() (gas: -6542 (-0.121%))
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -3620 (-0.122%))
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -4098 (-0.124%))
testExpansionOfKnotSet() (gas: -1363 (-0.128%))
testBatchDepositETHForStakingRevertsWhenInconsistentArrayLengthsAreSupplied() (gas: -25 (-0.145%))
testBurnLPTokensByBLSRevertsWhenArraysAreInconsistentLength() (gas: -25 (-0.146%))
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -4947 (-0.146%))
testStakeFreeFloatingReceiveETHAndThenClaim() (gas: -737 (-0.147%))
testThreeKnotsMultipleStakers() (gas: -1906 (-0.157%))
testDeployNewLiquidStakingDerivativeNetworkIsSuccessfulWithValidParams() (gas: -104 (-0.181%))
testOneKnotWithMultipleFreeFloatingStakers() (gas: -1580 (-0.182%))
testStakeKnotAndMintDerivatives() (gas: -5518 (-0.201%))
testTokenDeploymentIsSuccessfulWithCorrectParams() (gas: -194 (-0.560%))
testWithdrawETHForStakingWorksAsExpected() (gas: 64529 (0.790%))
testRegisterKeysAndWithdrawETHAsNodeRunner() (gas: -228138 (-35.692%))
Overall gas change: -208760 (-38.138%)
```

# empty blocks should be removed or emit something
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract

```bash

File: contracts/liquid-staking/LiquidStakingManager.sol
629:     receive() external payable {}  // @audit-gas empty block

File: contracts/liquid-staking/SyndicateRewardsProcessor.sol
98:     receive() external payable {} // @audit-gas empty block

File: contracts/smart-wallet/OwnableSmartWallet.sol
148:     receive() external payable { // @audit-gas empty block

File: contracts/syndicate/Syndicate.sol
352:     receive() external payable { // @audit-gas empty block

```

# remove unused functions
To save deployment size unused functions should be removed.

```bash
File: contracts/syndicate/Syndicate.sol
538:     function _calculateCollateralizedETHOwedPerKnot() internal view returns (uint256) { // @audit-gas function unused

File: contracts/syndicate/Syndicate.sol
545:     function _calculateNewAccumulatedETHPerCollateralizedShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) { // @audit-gas function unused

```

# change onlyOwner / admin / external functions not for endusers to payable
If a function is only callable by special users like admins it makes sense to add payable. The compiler removes checks for msg.value, saving approximately 20 gas per function call.

```
File: contracts/liquid-staking/GiantLP.sol
29:     function mint(address _recipient, uint256 _amount) external { // @audit-gas payable - OnlyPool

File: contracts/liquid-staking/GiantLP.sol
34:     function burn(address _recipient, uint256 _amount) external { // @audit-gas payable - OnlyPool

File: contracts/liquid-staking/LiquidStakingManager.sol
218:     function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {  // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LiquidStakingManager.sol
226:     function restoreFreeFloatingSharesToSmartWalletForRageQuit(
227:         address _smartWallet,
228:         bytes[] calldata _blsPublicKeys,
229:         uint256[] calldata _amounts
230:     ) external onlyDAO {  // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LiquidStakingManager.sol
239:     function updateDAOAddress(address _newAddress) external onlyDAO { // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LiquidStakingManager.sol
249:     function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO { // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LiquidStakingManager.sol
255:     function updateTicker(string calldata _newTicker) external onlyDAO { // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LiquidStakingManager.sol
267:     function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) { // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LiquidStakingManager.sol
278:     function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO { // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LiquidStakingManager.sol
289:     function rotateEOARepresentative(address _newRepresentative) external { // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LiquidStakingManager.sol
308:     function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO { // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LPToken.sol
46:     function mint(address _recipient, uint256 _amount) external onlyDeployer { // @audit-gas payable - onlyDAO

File: contracts/liquid-staking/LPToken.sol
46:     function mint(address _recipient, uint256 _amount) external onlyDeployer { // @audit-gas payable - onlyDeployer

File: contracts/liquid-staking/LPToken.sol
51:     function burn(address _recipient, uint256 _amount) external onlyDeployer { // @audit-gas payable - onlyDeployer

File: contracts/liquid-staking/SavETHVault.sol
200:     function withdrawETHForStaking(
201:         address _smartWallet,
202:         uint256 _amount
203:     ) public onlyManager nonReentrant returns (uint256) {  // @audit-gas payable - onlyManager

File: contracts/liquid-staking/StakingFundsVault.sol
56:     function updateDerivativesMinted() external onlyManager { // @audit-gas payable - onlyManager

File: contracts/liquid-staking/StakingFundsVault.sol
239:     function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) { // @audit-gas payable - onlyManager

File: contracts/liquid-staking/StakingFundsVault.sol
255:     function unstakeSyndicateSharesForRageQuit(
256:         address _sETHRecipient,
257:         bytes[] calldata _blsPublicKeys,
258:         uint256[] calldata _amounts
259:     ) external onlyManager nonReentrant { // @audit-gas payable - onlyManager

File: contracts/syndicate/Syndicate.sol
145:     function registerKnotsToSyndicate(
146:         bytes[] calldata _newBLSPublicKeyBeingRegistered
147:     ) external onlyOwner { // @audit-gas payable - onlyOwner

File: contracts/syndicate/Syndicate.sol
154:     function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner { // @audit-gas payable - onlyOwner

File: contracts/syndicate/Syndicate.sol
161:     function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner { // @audit-gas payable - onlyOwner

File: contracts/syndicate/Syndicate.sol
168:     function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner { // @audit-gas payable - onlyOwner

```

forge snapshot --diff
```bash
testExpansionOfKnotSet() (gas: -24 (-0.002%))
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -120 (-0.004%))
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -120 (-0.004%))
testFirstDepositsForKnotAreBetweenOneAndTwentyFour(uint256) (gas: -24 (-0.004%))
testStakeKnotAndMintDerivatives() (gas: -120 (-0.004%))
testFirstDepositDeploysAnLPToken() (gas: -24 (-0.004%))
testBurnLPRevertsWhenETHIsStaked() (gas: -24 (-0.004%))
testBurnLPRevertsWhenBalanceIsZero() (gas: -24 (-0.004%))
testReceiveETHAndDistributeDuringLPTransfers() (gas: -144 (-0.004%))
testReceiveETHAndDistributeToMultipleLPs() (gas: -144 (-0.005%))
testBatchDepositETHForStakingCanSuccessfullyDepositForMultipleValidators() (gas: -72 (-0.005%))
testBatchDepositETHForStakingRevertsWhenETHNotAttached() (gas: -72 (-0.005%))
testThreeKnotsMultipleStakers() (gas: -72 (-0.006%))
testThatAfterFirstKnotSecondKnotOnwardsJoinsTheLSDHouse() (gas: -336 (-0.006%))
testDepositETHForStaking() (gas: -72 (-0.007%))
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -216 (-0.007%))
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -240 (-0.007%))
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormationAndBalIncrease() (gas: -48 (-0.007%))
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormation() (gas: -48 (-0.008%))
testSecondDepositForKnotMintsSameLP() (gas: -48 (-0.008%))
testBurnLPForETHWorksAsExpected() (gas: -48 (-0.009%))
testBurnLPBeforeETHIsStaked() (gas: -48 (-0.009%))
testBurnLPTokensWorksAsExpectedForMultipleValidators() (gas: -144 (-0.010%))
testMint() (gas: -24 (-0.021%))
testBurn() (gas: -38 (-0.042%))
testCanReceiveETHAndWithdraw() (gas: -24 (-0.046%))
testDeployNewLiquidStakingDerivativeNetworkIsSuccessfulWithValidParams() (gas: -48 (-0.084%))
testWithdrawETHForStakingRevertsWhenAmountIsZero() (gas: -24 (-0.116%))
testWithdrawETHForStakingWorksAsExpected() (gas: -10469 (-0.127%))
testOnlySavETHVaultCanBurn() (gas: -24 (-0.149%))
testOnlySavETHVaultCanMint() (gas: -24 (-0.150%))
testWithdrawETHForStakingRevertsWhenNotManager() (gas: -24 (-0.185%))
Overall gas change: -12931 (-1.055%)
```

# use calldata instead of memory in functions that are external (public)
Use calldata instead of memory if the function argument is only read and the function is external

```
File: contracts/liquid-staking/StakingFundsVault.sol
355:     function claimFundsFromSyndicateForDistribution(bytes[] memory _blsPubKeys) external { // @audit-gas use calldata

File: contracts/smart-wallet/OwnableSmartWallet.sol
41:     function execute(address target, bytes memory callData)  // @audit-gas use calldata

function initialize(
        address _contractOwner,
        uint256 _priorityStakingEndBlock,
        address[] memory _priorityStakers,
        bytes[] memory _blsPubKeysForSyndicateKnots
    ) external virtual override initializer { // @audit-gas use calldata

```

forge snapshot --diff
```bash
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -250 (-0.008%))
testStakeKnotAndMintDerivatives() (gas: -250 (-0.009%))
testReceiveETHAndDistributeDuringLPTransfers() (gas: -300 (-0.009%))
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -300 (-0.010%))
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -360 (-0.011%))
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -370 (-0.011%))
testReceiveETHAndDistributeToMultipleLPs() (gas: -351 (-0.011%))
testThatAfterFirstKnotSecondKnotOnwardsJoinsTheLSDHouse() (gas: -602 (-0.011%))
testRegisterKeysAndWithdrawETHAsNodeRunner() (gas: -100 (-0.024%))
testWithdrawETHForStakingWorksAsExpected() (gas: -3000 (-0.037%))
Overall gas change: -5883 (-0.141%)
```

# splitting require() statements that use &&
Instead of using the && operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 8 GAS per &&

```bash
File: contracts/liquid-staking/LiquidStakingManager.sol
357:         require(_new != address(0) && _current != _new, "New is zero or current");
```

# variables that can be set to immutable / constant
If a variable is only set once and never changed, consider using immutable / constant for that variable.

```bash
File: contracts/liquid-staking/LiquidStakingManager.sol
158:     uint256 public constant MODULO = 100_00000; // @audit-gas constant

File: contracts/liquid-staking/LPTokenFactory.sol
15:     address public lpTokenImplementation; // @audit-gas immutable

File: contracts/liquid-staking/LSDNFactory.sol
15:     address public liquidStakingManagerImplementation; // @audit-gas immutable

File: contracts/liquid-staking/LSDNFactory.sol
21:     address public lpTokenFactory; // @audit-gas immutable

File: contracts/liquid-staking/LSDNFactory.sol
24:     address public smartWalletFactory; // @audit-gas immutable

File: contracts/liquid-staking/LSDNFactory.sol
27:     address public brand; // @audit-gas immutable

File: contracts/liquid-staking/LSDNFactory.sol
30:     address public savETHVaultDeployer; // @audit-gas immutable

File: contracts/liquid-staking/LSDNFactory.sol
33:     address public stakingFundsVaultDeployer; // @audit-gas immutable

File: contracts/liquid-staking/LSDNFactory.sol
36:     address public optionalGatekeeperDeployer; // @audit-gas immutable

File: contracts/liquid-staking/OptionalHouseGatekeeper.sol
12:     ILiquidStakingManager public liquidStakingManager; // @audit-gas immutable

File: contracts/liquid-staking/SavETHVaultDeployer.sol
13:     address implementation; // @audit-gas immutable

File: contracts/liquid-staking/StakingFundsVaultDeployer.sol
13:     address implementation; // @audit-gas immutable

File: contracts/syndicate/SyndicateFactory.sol
13:     address public  syndicateImplementation; // @audit-gas immutable

```

forge snapshot --diff
```bash
testThatAfterFirstKnotSecondKnotOnwardsJoinsTheLSDHouse() (gas: -4729 (-0.088%))
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -4366 (-0.129%))
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -4366 (-0.132%))
testReceiveETHAndDistributeDuringLPTransfers() (gas: -4366 (-0.135%))
testReceiveETHAndDistributeToMultipleLPs() (gas: -4366 (-0.137%))
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -4366 (-0.140%))
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -4366 (-0.147%))
testStakeKnotAndMintDerivatives() (gas: -4490 (-0.164%))
testBatchDepositETHForStakingCanSuccessfullyDepositForMultipleValidators() (gas: -2363 (-0.166%))
testBurnLPTokensWorksAsExpectedForMultipleValidators() (gas: -2363 (-0.168%))
testBatchDepositETHForStakingRevertsWhenETHNotAttached() (gas: -2363 (-0.169%))
testDepositETHForStaking() (gas: -2242 (-0.205%))
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormationAndBalIncrease() (gas: -2121 (-0.324%))
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormation() (gas: -2121 (-0.336%))
testSecondDepositForKnotMintsSameLP() (gas: -2121 (-0.343%))
testWithdrawETHForStakingWorksAsExpected() (gas: -29200 (-0.356%))
testFirstDepositsForKnotAreBetweenOneAndTwentyFour(uint256) (gas: -2121 (-0.386%))
testFirstDepositDeploysAnLPToken() (gas: -2121 (-0.387%))
testBurnLPForETHWorksAsExpected() (gas: -2121 (-0.389%))
testBurnLPRevertsWhenETHIsStaked() (gas: -2121 (-0.391%))
testBurnLPBeforeETHIsStaked() (gas: -2121 (-0.393%))
testBurnLPRevertsWhenBalanceIsZero() (gas: -2121 (-0.394%))
testSetupIsCorrect() (gas: -2121 (-21.765%))
Overall gas change: -95156 (-27.244%)
```

# storage pointer is cheaper than memory
Reference types cached in memory cost more gas than using storage pointers, as new memory is allocated for these variables, copying data from storage to memory.

```bash
File: contracts/liquid-staking/ETHPoolLPFactory.sol
85:         bytes memory blsPublicKeyOfPreviousKnot = KnotAssociatedWithLPToken[_oldLPToken]; // @audit-gas storage pointer is cheaper than memory
86:         bytes memory blsPublicKeyOfNewKnot = KnotAssociatedWithLPToken[_newLPToken]; // @audit-gas storage pointer is cheaper than memory

File: contracts/liquid-staking/SavETHVault.sol
229:         bytes memory blsPublicKeyOfKnot = KnotAssociatedWithLPToken[LPToken(_lpTokenAddress)]; // @audit-gas storage pointer

File: contracts/liquid-staking/StakingFundsVault.sol
179:         bytes memory blsPublicKeyOfKnot = KnotAssociatedWithLPToken[_lpToken];// @audit-gas storage pointer

File: contracts/liquid-staking/StakingFundsVault.sol
295:         bytes memory associatedBLSPublicKeyOfLpToken = KnotAssociatedWithLPToken[_token]; // @audit-gas storage pointer

```

forge snapshot --diff
```bash
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -45 (-0.001%))
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -45 (-0.001%))
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -90 (-0.003%))
testReceiveETHAndDistributeDuringLPTransfers() (gas: -180 (-0.006%))
testReceiveETHAndDistributeToMultipleLPs() (gas: -180 (-0.006%))
testBurnLPForETHWorksAsExpected() (gas: -39 (-0.007%))
testBurnLPTokensWorksAsExpectedForMultipleValidators() (gas: -112 (-0.008%))
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -499 (-0.016%))
testWithdrawETHForStakingWorksAsExpected() (gas: -200245 (-2.447%))
Overall gas change: -201435 (-2.495%)
```

# use unchecked if no over- / underflow is possible
If it's not possible that the equation will over- / underflow, especially if it was checked previously it's better to use an unchecked{} block to save gas.

```bash
File: contracts/liquid-staking/ETHPoolLPFactory.sol
63:         for (uint256 i; i < numOfRotations; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/ETHPoolLPFactory.sol
145:             numberOfLPTokensIssued++; // @audit-gas unchecked would need more than uint256.max function calls to overflow

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
38:         for (uint256 i; i < numOfVaults; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
65:         for (uint256 i; i < numOfVaults; ++i) {// @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
92:         for (uint256 i; i < _stakingFundsVaults.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
120:         for (uint256 i; i < numOfRotations; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
139:         for (uint256 i; i < numOfVaults; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
188:         for (uint256 i; i < _lpTokens.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantPoolBase.sol
57:         idleETH -= _amount; // @audit-gas unchecked, is checked in require idleETH >= _amount

File: contracts/liquid-staking/GiantPoolBase.sol
76:         for (uint256 i; i < amountOfTokens; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
42:         for (uint256 i; i < numOfSavETHVaults; ++i) {// @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
78:         for (uint256 i; i < numOfVaults; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
82:             for (uint256 j; j < _lpTokens[i].length; ++j) { // @audit-gas use unchecked{j++;} in end of loop

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
128:         for (uint256 i; i < numOfRotations; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
146:         for (uint256 i; i < numOfVaults; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
148:             for (uint256 j; j < _lpTokens[i].length; ++j) { // @audit-gas use unchecked{j++;} in end of loop

File: contracts/liquid-staking/LiquidStakingManager.sol
392:         for (uint256 i; i < _blsPubKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/LiquidStakingManager.sol
465:         for (uint256 i; i < len; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/LiquidStakingManager.sol
538:         for (uint256 i; i < numOfValidators; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/LiquidStakingManager.sol
588:         for (uint256 i; i < numOfKnotsToProcess; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/SavETHVault.sol
63:         for (uint256 i; i < numOfValidators; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/SavETHVault.sol
103:         for (uint256 i; i < numOfTokens; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/SavETHVault.sol
116:         for (uint256 i; i < numOfTokens; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/StakingFundsVault.sol
78:         for (uint256 i; i < numOfValidators; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/StakingFundsVault.sol
152:         for (uint256 i; i < numOfTokens; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/StakingFundsVault.sol
166:         for (uint256 i; i < numOfTokens; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/StakingFundsVault.sol
203:         for (uint256 i; i < _blsPubKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/StakingFundsVault.sol
266:         for (uint256 i; i < _blsPublicKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/StakingFundsVault.sol
276:         for (uint256 i; i < _blsPubKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/liquid-staking/StakingFundsVault.sol
286:         for (uint256 i; i < _token.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
211:         for (uint256 i; i < _blsPubKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
259:         for (uint256 i; i < _blsPubKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
273:             sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount; // @audit-gas use unchecked, it's checked in if that its bigger

File: contracts/syndicate/Syndicate.sol
301:         for (uint256 i; i < _blsPubKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
346:         for (uint256 i; i < _blsPubKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
420:         for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
513:                     for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
561:         for (uint256 i; i < knotsToRegister; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
586:         for (uint256 i; i < _priorityStakers.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
599:         for (uint256 i; i < _blsPublicKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

File: contracts/syndicate/Syndicate.sol
649:         for (uint256 i; i < _blsPubKeys.length; ++i) { // @audit-gas use unchecked{i++;} in end of loop

```

forge snapshot --diff
```bash
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormationAndBalIncrease() (gas: -62 (-0.010%))
testBurnLPForDerivativeETHFromStakehouseAfterKnotFormation() (gas: -62 (-0.010%))
testSecondDepositForKnotMintsSameLP() (gas: -62 (-0.010%))
testFirstDepositsForKnotAreBetweenOneAndTwentyFour(uint256) (gas: -62 (-0.011%))
testFirstDepositDeploysAnLPToken() (gas: -62 (-0.011%))
testDepositETHForStaking() (gas: -124 (-0.011%))
testBurnLPRevertsWhenETHIsStaked() (gas: -62 (-0.011%))
testBurnLPBeforeETHIsStaked() (gas: -62 (-0.012%))
testBurnLPRevertsWhenBalanceIsZero() (gas: -62 (-0.012%))
testStakeKnotAndMintDerivatives() (gas: -434 (-0.016%))
testRegisterKeysAndWithdrawETHAsNodeRunner() (gas: -66 (-0.016%))
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -626 (-0.021%))
testBurnLPForETHWorksAsExpected() (gas: -125 (-0.023%))
testReceiveETHAndDistributeDuringLPTransfers() (gas: -755 (-0.023%))
testClaimAsCollateralizedSlotOwner() (gas: -66 (-0.025%))
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -837 (-0.025%))
testReceiveETHAndDistributeToMultipleLPs() (gas: -818 (-0.026%))
testBatchDepositETHForStakingCanSuccessfullyDepositForMultipleValidators() (gas: -375 (-0.026%))
testBatchDepositETHForStakingRevertsWhenETHNotAttached() (gas: -375 (-0.027%))
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -851 (-0.027%))
testThatAfterFirstKnotSecondKnotOnwardsJoinsTheLSDHouse() (gas: -1781 (-0.033%))
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -1116 (-0.033%))
testBothCollateralizedAndSlotClaim() (gas: -186 (-0.034%))
testBurnLPTokensWorksAsExpectedForMultipleValidators() (gas: -564 (-0.040%))
testThreeKnotsMultipleStakers() (gas: -624 (-0.052%))
testExpansionOfKnotSet() (gas: -570 (-0.054%))
testOneKnotWithMultipleFreeFloatingStakers() (gas: -690 (-0.080%))
testStakeFreeFloatingReceiveETHAndThenClaim() (gas: -514 (-0.103%))
testWithdrawETHForStakingWorksAsExpected() (gas: -23244 (-0.291%))
Overall gas change: -35237 (-1.074%)
```

# caching variables - if a storage variable is access more than once, save it to a local variable
Every additional access to a storage variable will cost 100 gas, try to avoide accessing the same storage variable more than once per function - especially in loops.

```bash
File: contracts/liquid-staking/GiantMevAndFeesPool.sol
099:         // @audit-gas multiasccess statevar - define GiantLP _lpTokenETH = lpTokenETH; and use it in return values
100:         return _previewAccumulatedETH(_user, address(lpTokenETH), lpTokenETH.balanceOf(_user), lpTokenETH.totalSupply(), accumulated);

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
209:         // @audit-gas multiasccess statevar - define GiantLP _lpTokenETH = lpTokenETH; and use it in return values
210:         claimed[_user][address(lpTokenETH)] = (accumulatedETHPerLPShare * lpTokenETH.balanceOf(_user)) / PRECISION;

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
95:                 lpTokenETH.burn(msg.sender, amount); // @audit-gas multiasccess statevar - define GiantLP _lpTokenETH = lpTokenETH; outside of loop and change at require and here

File: contracts/liquid-staking/LiquidStakingManager.sol
270:         emit WhitelistingStatusChanged(msg.sender, enableWhitelisting); // @audit-gas multiasccess statevar - use local variable _changeWhitelist instead of storage also in return

File: contracts/liquid-staking/LiquidStakingManager.sol
299:         _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false); // @audit-gas  multiasccess statevar - accessing two times smartWalletRepresentative[smartWallet] in function, replace with local var

File: contracts/liquid-staking/LiquidStakingManager.sol
317:         _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false); // @audit-gas multiasccess statevar - accessing two times smartWalletRepresentative[smartWallet] in function, replace with local var

File: contracts/liquid-staking/LiquidStakingManager.sol
790:         string memory lowerTicker = IBrandNFT(brand).toLowerCase(stakehouseTicker); // @audit-gas multiasccess statevar - brand accessed 2 times, move to local address _brand

File: contracts/liquid-staking/LiquidStakingManager.sol
829:             abi.encodeWithSelector(
830:                 ITransactionRouter.createStakehouse.selector,
831:                 associatedSmartWallet,
832:                 _blsPublicKeyOfKnot,
833:                 stakehouseTicker, // @audit-gas multiasccess statevar - stakehouseTicker access 2 times, move to string storage _stakehouseTicker = stakehouseTicker; and use _stakehouseTicker

File: contracts/liquid-staking/LiquidStakingManager.sol
844:         stakehouse = getStakeHouseUniverse().memberKnotToStakeHouse(_blsPublicKeyOfKnot); // @audit-gas multiasccess statevar - stakehouse 4x access in function, save in local address, save it once on stakehouse and use for rest local var

File: contracts/liquid-staking/LiquidStakingManager.sol
926:         if (daoCommissionPercentage > 0) { // @audit-gas multiasccess statevar - daoCommissionPercentage accessed 2 times in function, move to local

```

forge snapshot --diff
```bash
testThatAfterFirstKnotSecondKnotOnwardsJoinsTheLSDHouse() (gas: -452 (-0.008%))
testReceiveETHAndDistributeDuringLPTransfers() (gas: -273 (-0.008%))
testReceiveETHAndDistributeToMultipleLPs() (gas: -273 (-0.009%))
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -273 (-0.009%))
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -341 (-0.010%))
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -349 (-0.011%))
testStakeKnotAndMintDerivatives() (gas: -373 (-0.014%))
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -636 (-0.020%))
testDeployNewLiquidStakingDerivativeNetworkIsSuccessfulWithValidParams() (gas: -134 (-0.234%))
Overall gas change: -3104 (-0.323%)
```

# shift right/left instead of division/multiplication if possible
If no over- / underflow is possible and u have a muliplication/division by a multiple from 2 use a shift.  
**Care!** division by 0 and over- / underflow are not checked.

```bash
File: contracts/syndicate/Syndicate.sol
378:         return ethPerKnot / 2; // @audit-gas use shift >> 1
```

forge snapshot --diff
```bash
testETHSuppliedFromGiantPoolCanBeUsedInFactoryDeployedLSDN() (gas: -59 (-0.002%))
testStakeKnotAndMintDerivatives() (gas: -59 (-0.002%))
testDAOCanCoordinateRageQuitOfOnlyKnotInNetwork() (gas: -236 (-0.007%))
testReceiveETHAndDistributeToLPsLinkedToKnotsThatMintedDerivatives() (gas: -295 (-0.010%))
testThatAfterFirstKnotSecondKnotOnwardsJoinsTheLSDHouse() (gas: -767 (-0.014%))
testCreateFirstKnotAndReceiveSyndicateRewards() (gas: -531 (-0.016%))
testReceiveETHAndDistributeDuringLPTransfers() (gas: -531 (-0.016%))
testReceiveETHAndDistributeToMultipleLPs() (gas: -531 (-0.017%))
testClaimAsCollateralizedSlotOwner() (gas: -59 (-0.023%))
testBothCollateralizedAndSlotClaim() (gas: -295 (-0.054%))
testThreeKnotsMultipleStakers() (gas: -1298 (-0.107%))
testOneKnotWithMultipleFreeFloatingStakers() (gas: -1239 (-0.143%))
testExpansionOfKnotSet() (gas: -1829 (-0.173%))
testStakeFreeFloatingReceiveETHAndThenClaim() (gas: -944 (-0.189%))
Overall gas change: -8673 (-0.774%)
```

# use bytes32 if string fits in
You should use bytes32 instead of string if you store a string that will fit in bytes32.

```bash
File: contracts/liquid-staking/LiquidStakingManager.sol
110:     /// @notice string name for the stakehouse 3-5 characters long
111:     string public stakehouseTicker; // @audit-gas change to bytes32
```
Comment states that it's only 3-5 characters, in cases like this, consider to use bytes32.

# <x> += <y> costs more gas than <x> = <x> + <y> for state variables
Using the addition operator instead of plus-equals saves gas.

```bash
File: contracts/liquid-staking/GiantPoolBase.sol
39:         idleETH += msg.value; // @audit-gas change to idleETH = idleETH + msg.value;

File: contracts/liquid-staking/LiquidStakingManager.sol
771:         stakedKnotsOfSmartWallet[smartWallet] += 1;

File: contracts/liquid-staking/LiquidStakingManager.sol
783:         numberOfKnots += 1;

File: contracts/liquid-staking/LiquidStakingManager.sol
841:         numberOfKnots += 1;

File: contracts/liquid-staking/SavETHVault.sol
71:             totalAmount += amount;

File: contracts/liquid-staking/StakingFundsVault.sol
58:         totalShares += 4 ether; 

File: contracts/liquid-staking/StakingFundsVault.sol
97:             totalAmount += amount;

File: contracts/liquid-staking/StakingFundsVault.sol
278:             totalAccumulated += previewAccumulatedETH(_user, token);

File: contracts/liquid-staking/StakingFundsVault.sol
287:             totalAccumulated += previewAccumulatedETH(_user, _token[i]);

File: contracts/liquid-staking/SyndicateRewardsProcessor.sol
65:                 totalClaimed += due;

File: contracts/liquid-staking/SyndicateRewardsProcessor.sol
85:                 accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;

File: contracts/syndicate/Syndicate.sol
190:             accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;

File: contracts/syndicate/Syndicate.sol
185:                 accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);

File: contracts/syndicate/Syndicate.sol
227:             sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;

File: contracts/syndicate/Syndicate.sol
227:             sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;

File: contracts/syndicate/Syndicate.sol
227:             sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;

File: contracts/syndicate/Syndicate.sol
317:                 totalClaimed += unclaimedUserShare;

File: contracts/syndicate/Syndicate.sol
430:                     currentAccrued +=
431:                         balance * unprocessedForKnot / (4 ether - currentSlashedAmount);

File: contracts/syndicate/Syndicate.sol
521:                         accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
522:                             balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);

File: contracts/syndicate/Syndicate.sol
521:                         accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
522:                             balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount); 

File: contracts/syndicate/Syndicate.sol
558:         numberOfRegisteredKnots += knotsToRegister;

File: contracts/syndicate/Syndicate.sol
659:                 totalClaimed += unclaimedUserShare;

```

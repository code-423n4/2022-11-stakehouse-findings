# Report
## Low Risk ##
### [L-1]: Missing checks for address(0x0)
**Context:**

1. ```pool = _pool;``` [L25](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L25) 
1. ```transferHookProcessor = ITransferHookProcessor(_transferHookProcessor);``` [L26](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L26) (_transferHookProcessor)

**Recommendation:**

Add non-zero address checks when set address state variables.
## Non-Critical Issues ##
### [N-1]: Use double quotes for string literals

 ```require(owner != address(0), 'Wallet cannot be address 0');``` [L37](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L37)
 
Double quotes are used in all contracts except here.

### [N-2]: Use mixedCase
**Context:**

1. ```uint256 public MODULO = 100_00000;``` [L158](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158) (variable name must be mixedCase)
1. ```mapping(LPToken => bytes) public KnotAssociatedWithLPToken;``` [L47](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L47) (variable name must be in mixedCase)

### [N-3]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return (rest, daoAmount);``` [L927](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L927) 
1. ```return (_received, 0);``` [L930](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L930) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.


### [N-4]: Wrong order of functions
**Context:**

1. [SyndicateFactory.calculateSyndicateDeploymentAddress](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L48)  (external function can not go after public function)
1. [GiantPoolBase.withdrawETH](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L52) (external function can not go after public function)
1. [GiantSavETHVaultPool.withdrawDETH](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L66) (external function can not go after public function)
1. [OwnableSmartWallet.transferOwnership](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L94) (public function can not go after public view function)
1. [OwnableSmartWallet.setApproval](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L114) (external function can not go after public function)
1. [OwnableSmartWallet.isTransferApproved](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L139) (public function can not go after internal function)
1. [OwnableSmartWallet.receive](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L148) (receive function must go after constructor and before external functions)
1. [SavETHVault.burnLPTokensByBLS](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L99) (external function can not go after public function)
1. [SavETHVault.isDETHReadyForWithdrawal](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L228) (external function can not go after public function)
1. [GiantMevAndFeesPool.batchRotateLPTokens](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L105) (external function can not go after external view function)
1. [GiantMevAndFeesPool.beforeTokenTransfer](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L146) (external function can not go after public function)
1. [StakingFundsVault.batchDepositETHForStaking](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L69) (external function can not go after public function)
1. [StakingFundsVault.burnLPTokensForETHByBLS](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L148) (external function can not go after public function)
1. [StakingFundsVault.claimRewards](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L199) (external function can not go after public function)
1. [StakingFundsVault.unstakeSyndicateSharesForRageQuit](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L255) (external function can not go after public function)
1. [StakingFundsVault.beforeTokenTransfer](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L315) (external function can not go after public function)
1. [Syndicate.stake](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L203) (external function can not go after public function)
1. [Syndicate.updateCollateralizedSlotOwnersAccruedETH](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L337) (external function can not go after public function)
1. [Syndicate.receive](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L352) (receive function must go after constructor and before external functions)
1. [Syndicate.previewUnclaimedETHAsFreeFloatingStaker](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L384) (external function can not go after public function)
1. [LiquidStakingManager.stake](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L524) (external function can not go after public function)
1. [LiquidStakingManager.receive](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L629) (receive function must go after constructor and before external functions)
1. [LiquidStakingManager._updateDAORevenueCommission](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L948) (internal function can not go after internal view function)
1. [SyndicateRewardsProcessor.totalRewardsReceived](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L93) (public function can not go after internal function)
1. [SyndicateRewardsProcessor.receive](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98) (receive function must go after constructor and before external functions)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-5]: Public functions can be external
**Context:**

1. [GiantPoolBase.depositETH](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L34) 
1. [LSDNFactory.deployNewLiquidStakingDerivativeNetwork](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L73) 
1. [GiantSavETHVaultPool.batchDepositETHForStaking](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L29) 
1. [SavETHVault.depositETHForStaking](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L83) 
1. [SavETHVault.withdrawETHForStaking](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L200) 
1. [StakingFundsVault.depositETHForStaking](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L113)
1. [StakingFundsVault.withdrawETH](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L239) 
1. [Syndicate.claimAsStaker](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L285)
1. [Syndicate.calculateNewAccumulatedETHPerCollateralizedSharePerKnot](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L458) 
1. [LiquidStakingManager.isKnotDeregistered](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L514) 

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.

### [N-6]: NatSpec is missing
**Context:**

1. ```function createWallet() external returns (address wallet) {``` [L28](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L28) 
1. ```function createWallet(address owner) external returns (address wallet) {``` [L32](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L32) 
1. ```function _createWallet(address owner) internal returns (address wallet) {``` [L36](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L36) 
1. ```function mint(address _recipient, uint256 _amount) external {``` [L29](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L29) 
1. ```function burn(address _recipient, uint256 _amount) external {``` [L34](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L34) 
1. ```function _beforeTokenTransfer(address _from, address _to, uint256 _amount) internal override {``` [L39](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L39) 
1. ```function _afterTokenTransfer(address _from, address _to, uint256 _amount) internal override {``` [L43](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L43) 
1. ```function init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) external virtual initializer {``` [L45](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L45) 
1. ```function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {``` [L491](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L491) 
1. ```function _calculateCollateralizedETHOwedPerKnot() internal view returns (uint256) {``` [L538](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L538) 
1. ```function _initStakingFundsVault(address _stakingFundsVaultDeployer, address _tokenFactory) internal virtual {``` [L911](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L911) 

### [N-7]: Typos
**Context:**

1. ```require(numOfTokens == _amounts.length, "Inconsisent array length");``` [L115](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L115) (Change ***Inconsisent*** to ***Inconsistent***)
1. ```/// @notice Utility function that determins whether an LP can be burned for dETH if the associated derivatives have been minted``` [L227](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L227) (Change ***determins*** to ***determines***)
1. ```// register validtor initals for each of the KNOTs``` [L476](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L476) (Change ***validtor*** to ***validator***)
1. ```// register validtor initals for each of the KNOTs``` [L476](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L476) (Change ***initals*** to ***initials***)
1. ```/// @param _newLPToken Instane of the new LP token (to be minted)``` [L74](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L74) (Change ***Instane*** to ***Instance***)
1. ```// mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied``` [L124](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L124) (Change ***depoistor*** to ***depositor***)
1. ```// mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied``` [L150](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L150) (Change ***depoistor*** to ***depositor***)

### [N-8]: TODO
**Context:**

```// todo - check else case for any ETH lost``` [L195](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L195) 

### [N-9]: Line is too long
**Context:**

1. ```if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).beforeTokenTransfer(_from, _to, _amount);``` [L40](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L40) 
1. ```if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).afterTokenTransfer(_from, _to, _amount);``` [L46](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L46) 
1. ```import { ERC20PermitUpgradeable } from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-ERC20PermitUpgradeable.sol";``` [L6](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L6) 
1. ```if (address(transferHookProcessor) != address(0)) transferHookProcessor.beforeTokenTransfer(_from, _to, _amount);``` [L62](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L62) 
1. ```function batchDepositETHForStaking(bytes[] calldata _blsPublicKeyOfKnots, uint256[] calldata _amounts) external payable {``` [L57](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L57) 
1. ```require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");``` [L64](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L64) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,``` [L66](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L66) 
1. ```function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) {``` [L83](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L83) 
1. ```require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");``` [L84](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L84) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,``` [L86](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L86) 
1. ```IDataStructures.LifecycleStatus validatorStatus = getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfKnot);``` [L132](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L132) 
1. ```uint256 dETHBalance = getSavETHRegistry().knotDETHBalanceInIndex(indexOwnedByTheVault, blsPublicKeyOfKnot);``` [L158](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L158) 
1. ```getSavETHRegistry().addKnotToOpenIndex(liquidStakingManager.stakehouse(), blsPublicKeyOfKnot, address(this));``` [L165](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L165) 
1. ```IDataStructures.LifecycleStatus validatorStatus = getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfKnot);``` [L230](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L230) 
1. ```/// @notice Stake ETH against multiple BLS keys within multiple LSDNs and specify the amount of ETH being supplied for each key``` [L22](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L22) 
1. ```return _previewAccumulatedETH(_user, address(lpTokenETH), lpTokenETH.balanceOf(_user), lpTokenETH.totalSupply(), accumulated);``` [L97](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L97) 
1. ```function batchDepositETHForStaking(bytes[] calldata _blsPublicKeyOfKnots, uint256[] calldata _amounts) external payable {``` [L69](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L69) 
1. ```require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");``` [L79](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L79) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,``` [L81](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L81) 
1. ```claimed[msg.sender][address(tokenForKnot)] = (tokenForKnot.balanceOf(msg.sender) * accumulatedETHPerLPShare) / PRECISION;``` [L103](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L103) 
1. ```function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) {``` [L113](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L113) 
1. ```require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");``` [L114](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L114) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,``` [L116](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L116) 
1. ```claimed[msg.sender][address(tokenForKnot)] = (tokenForKnot.balanceOf(msg.sender) * accumulatedETHPerLPShare) / PRECISION;``` [L140](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L140) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,``` [L181](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L181) 
1. ```/// @notice Any LP tokens for BLS keys that have had their derivatives minted can claim ETH from the syndicate contract``` [L197](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L197) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(_blsPubKeys[i]) == IDataStructures.LifecycleStatus.TOKENS_MINTED,``` [L211](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L211) 
1. ```if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {``` [L215](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L215) 
1. ```// If a partial list of BLS keys that have free floating staked are supplied, then partial funds accrued will be fetched``` [L217](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L217) 
1. ```require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");``` [L230](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L230) 
1. ```/// @param _blsPublicKeys List of BLS public keys being processed (assuming DAO only has BLS pub keys from correct smart wallet)``` [L253](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L253) 
1. ```/// @notice Preview total ETH accumulated by a staking funds LP token holder associated with many KNOTs that have minted derivatives``` [L273](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L273) 
1. ```function batchPreviewAccumulatedETHByBLSKeys(address _user, bytes[] calldata _blsPubKeys) external view returns (uint256) {``` [L274](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L274) 
1. ```/// @notice Preview total ETH accumulated by a staking funds LP token holder associated with many KNOTs that have minted derivatives``` [L283](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L283) 
1. ```/// @notice Preview total ETH accumulated by a staking funds LP token holder associated with a KNOT that has minted derivatives``` [L292](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L292) 
1. ```if (getAccountManager().blsPublicKeyToLifecycleStatus(associatedBLSPublicKeyOfLpToken) != IDataStructures.LifecycleStatus.TOKENS_MINTED) {``` [L296](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L296) 
1. ```// Looking at this contract balance and the ETH that is yet to be transferred from the syndicate, then tell the user how much ETH they have earned``` [L300](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L300) 
1. ```if (getAccountManager().blsPublicKeyToLifecycleStatus(blsPubKey) == IDataStructures.LifecycleStatus.TOKENS_MINTED) {``` [L322](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L322) 
1. ```// in case the new user has existing rewards - give it to them so that the after transfer hook does not wipe pending rewards``` [L336](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L336) 
1. ```/// @param _blsPubKeys List of BLS public key identifiers of validators that have sETH staked in the syndicate for the vault``` [L354](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L354) 
1. ```/// @dev This contract can be extended to allow lending and borrowing of time slots for borrower to redeem any revenue generated within the specified window``` [L35](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L35) 
1. ```event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);``` [L63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L63) 
1. ```/// @notice Total accrued ETH for all collateralized SLOT holders per knot which is then distributed based on individual balances``` [L71](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L71) 
1. ```/// @notice Block number after which if there are sETH staking slots available, it can be supplied by anyone on the market``` [L92](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L92) 
1. ```/// @notice Syndicate deployer can highlight addresses that get priority for staking free floating house sETH up to a certain block before anyone can do it``` [L95](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L95) 
1. ```/// @notice Whether a BLS public key, that has been previously registered, is no longer part of the syndicate and its shares (free floating or SLOT) cannot earn any more rewards``` [L116](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L116) 
1. ```/// @notice Once a BLS public key is no longer part of the syndicate, the accumulated ETH per free floating SLOT share is snapshotted so historical earnings can be drawn down correctly``` [L119](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L119) 
1. ```/// @param _priorityStakers Optional list of addresses that will have priority for staking sETH against each knot registered``` [L127](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L127) 
1. ```/// @param _blsPubKeysForSyndicateKnots List of BLS public keys of Stakehouse protocol registered KNOTs participating in syndicate``` [L128](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L128) 
1. ```/// @notice Make knot shares of a registered list of BLS public keys inactive - the action cannot be undone and no further ETH accrued``` [L153](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L153) 
1. ```/// @notice Should this block be in the future, it means only those listed in the priority staker list can stake sETH``` [L166](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L166) 
1. ```/// @notice Update accrued ETH per SLOT share without distributing ETH as users of the syndicate individually pull funds``` [L173](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L173) 
1. ```// Ensure there are registered KNOTs. Syndicates are deployed with at least 1 registered but this can fall to zero.``` [L175](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L175) 
1. ```accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);``` [L185](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L185) 
1. ```uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);``` [L189](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L189) 
1. ```/// @param _sETHAmounts Per BLS public key, the total amount of sETH that will be staked (up to 4 collateralized SLOT per KNOT)``` [L201](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L201) 
1. ```/// @param _onBehalfOf Allows a caller to specify an address that will be assigned stake ownership and rights to claim``` [L202](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L202) 
1. ```if (!isKnotRegistered[_blsPubKey] || isNoLongerPartOfSyndicate[_blsPubKey]) revert KnotIsNotRegisteredWithSyndicate();``` [L216](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L216) 
1. ```sETHUserClaimForKnot[_blsPubKey][_onBehalfOf] = (_sETHAmount * accumulatedETHPerFreeFloatingShare) / PRECISION;``` [L228](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L228) 
1. ```// This is designed to cope with falling SLOT balances i.e. when collateralized SLOT is burnt after applying penalties``` [L311](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L311) 
1. ```/// @notice For any new ETH received by the syndicate, at the knot level allocate ETH owed to each collateralized owner``` [L335](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L335) 
1. ```/// @notice For any new ETH received by the syndicate, at the knot level allocate ETH owed to each collateralized owner and do it for a batch of knots``` [L341](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L341) 
1. ```/// @notice Calculate the amount of unclaimed ETH for a given BLS publice key + free floating SLOT staker without factoring in unprocessed rewards``` [L356](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L356) 
1. ```function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {``` [L359](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L359) 
1. ```/// @notice Using `highestSeenBalance`, this is the amount that is separately allocated to either free floating or collateralized SLOT holders``` [L372](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L372) 
1. ```/// @notice Preview the amount of unclaimed ETH available for an sETH staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract``` [L381](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L381) 
1. ```currentAccumulatedETHPerFreeFloatingShare + calculateNewAccumulatedETHPerFreeFloatingShare();``` [L390](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L390) 
1. ```/// @notice Preview the amount of unclaimed ETH available for a collatearlized SLOT staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract``` [L398](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L398) 
1. ```+ ((calculateETHForFreeFloatingOrCollateralizedHolders() - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);``` [L407](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L407) 
1. ```uint256 numberOfCollateralisedSlotOwnersForKnot = getSlotRegistry().numberOfCollateralisedSlotOwnersForKnot(_blsPubKey);``` [L416](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L416) 
1. ```return ((calculateETHForFreeFloatingOrCollateralizedHolders() - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);``` [L447](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L447) 
1. ```/// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this amongs the current set of collateralized owners (a dynamic set of addresses and balances)``` [L490](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L490) 
1. ```// Don't allocate ETH when the current slashed amount is four. Syndicate will wait until ETH is topped up to claim revenue``` [L503](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L503) 
1. ```// This copes with increasing numbers of collateralized slot owners and also copes with SLOT that has been slashed but not topped up``` [L505](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L505) 
1. ```uint256 numberOfCollateralisedSlotOwnersForKnot = getSlotRegistry().numberOfCollateralisedSlotOwnersForKnot(_blsPubKey);``` [L506](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L506) 
1. ```accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;``` [L511](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L511) 
1. ```// if the knot is no longer active, no further accrual of rewards are possible snapshots are possible but ETH accrued up to that point``` [L531](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L531) 
1. ```/// @dev Business logic for de-registering a set of knots from the syndicate and doing the required snapshots to ensure historical earnings are preserved``` [L596](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L596) 
1. ```event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);``` [L66](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L66) 
1. ```/// @notice In preparation of a rage quit, restore sETH to a smart wallet which are recoverable with the execution methods in the event this step does not go to plan``` [L222](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L222) 
1. ```/// @param _blsPublicKeys List of BLS public keys being processed (assuming DAO only has BLS pub keys from correct smart wallet)``` [L224](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L224) 
1. ```require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");``` [L280](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L280) 
1. ```/// @notice Allow node runners to withdraw ETH from their smart wallet. ETH can only be withdrawn until the KNOT has not been staked.``` [L323](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L323) 
1. ```require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");``` [L328](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L328) 
1. ```require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");``` [L331](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L331) 
1. ```require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");``` [L332](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L332) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,``` [L335](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L335) 
1. ```function rotateNodeRunnerOfSmartWallet(address _current, address _new, bool _wasPreviousNodeRunnerMalicious) external {``` [L356](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L356) 
1. ```require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");``` [L393](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L393) 
1. ```require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");``` [L396](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L396) 
1. ```// Ensure that the node runner does not whitelist multiple EOA representatives - they can only have 1 active at a time``` [L453](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L453) 
1. ```require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");``` [L455](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L455) 
1. ```require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");``` [L469](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L469) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKey) == IDataStructures.LifecycleStatus.UNBEGUN,``` [L472](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L472) 
1. ```return !isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot) || bannedBLSPublicKeys[_blsPublicKeyOfKnot] != address(0);``` [L501](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L501) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(blsPubKey) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,``` [L546](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L546) 
1. ```/// @notice Anyone can call this to trigger creating a knot which will mint derivatives once the balance has been reported``` [L573](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L573) 
1. ```/// @param _blsPublicKeyOfKnots List of BLS public keys registered with the network becoming knots and minting derivatives``` [L574](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L574) 
1. ```require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");``` [L589](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L589) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(_blsPublicKeyOfKnots[i]) == IDataStructures.LifecycleStatus.DEPOSIT_COMPLETED,``` [L593](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L593) 
1. ```require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");``` [L82](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L82) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfPreviousKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,``` [L92](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L92) 
1. ```getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,``` [L97](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L97) 
1. ```function _depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount, bool _enableTransferHook) internal {``` [L110](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L110) 
1. ```require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");``` [L122](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L122) 
1. ```LPToken(lpTokenFactory.deployLPToken(address(this), address(this), tokenSymbol, tokenName)) :``` [L140](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L140) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).

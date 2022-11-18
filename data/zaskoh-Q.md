# Solidity pragma versioning
Solidity pragma versioning should be exactly same in all contracts. Currently some contracts use ^0.8.13 but some are fixed to 0.8.13

# solc version (pragma versioning)
Solidity pragma versioning can be upgraded to latest version 0.8.17 as this version has some bugs fixed that still exist in 0.8.13 - https://docs.soliditylang.org/en/v0.8.17/bugs.html

To Upgrade to 0.8.17 you need to remove the stakehouse dependency ("@blockswaplab/stakehouse-solidity-api": "2.1.0") as you need to change the Version to 0.8.17 in the Interfaces.
The [stakehouse-contract-interfaces](https://github.com/stakehouse-dev/stakehouse-contract-interfaces/tree/main/contracts/interfaces) has a wrong pragma definition (pragma solidity 0.8.13) and so you need to import them locally and change it to 0.8.17 / ^0.8.13.  
Probably it's best to open a PR to change it on the original site.

```bash
➜  2022-11-stakehouse git:(main) ✗ forge test
[⠢] Compiling...
[⠒] Compiling 100 files with 0.8.17
[⠊] Solc 0.8.17 finished in 11.85s
Compiler run successful
```

All tests also pass.

# Upgrade open zeppelin contract dependency
An outdated OZ version is used (which has known vulnerabilities, see https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

Currently used:
```bash
    "@openzeppelin/contracts": "^4.5.0",
    "@openzeppelin/contracts-upgradeable": "4.5.0",
```
Consider updating to > 4.7.3 / 4.8

# Layout of a Solidity Source File
For a better developer experience you should stick to the [solidity-docs](https://docs.soliditylang.org/en/v0.8.17/layout-of-source-files.html) specification how to structure a Solidity Source File.  
Some files mixed up the order of the licencse => pragmas => imports => rest definition.

```bash
File: contracts/liquid-staking/GiantLP.sol
1: pragma solidity ^0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { ERC20 } from "@openzeppelin/contracts/token/ERC20/ERC20.sol";
6: import { ITransferHookProcessor } from "../interfaces/ITransferHookProcessor.sol";

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
1: pragma solidity ^0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { GiantLP } from "./GiantLP.sol";

File: contracts/liquid-staking/GiantPoolBase.sol
1: pragma solidity ^0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
1: pragma solidity ^0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { StakehouseAPI } from "@blockswaplab/stakehouse-solidity-api/contracts/StakehouseAPI.sol";

File: contracts/liquid-staking/LPToken.sol
1: pragma solidity ^0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

File: contracts/liquid-staking/LPTokenFactory.sol
1: pragma solidity ^0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";

File: contracts/liquid-staking/LSDNFactory.sol
1: pragma solidity 0.8.17;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";

File: contracts/liquid-staking/OptionalHouseGatekeeper.sol
1: pragma solidity ^0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { IGateKeeper } from "../interfaces/IGateKeeper.sol";

File: contracts/liquid-staking/SavETHVaultDeployer.sol
1: pragma solidity ^0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";

File: contracts/syndicate/Syndicate.sol
1: pragma solidity 0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

File: contracts/syndicate/SyndicateErrors.sol
1: pragma solidity 0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: error ZeroAddress();

File: contracts/syndicate/SyndicateFactory.sol
1: pragma solidity 0.8.13;
2: 
3: // SPDX-License-Identifier: MIT
4: 
5: import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";
```

## Event is missing indexed fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question.

```bash
File: contracts/liquid-staking/ETHPoolLPFactory.sol
16:     event ETHWithdrawnByDepositor(address depositor, uint256 amount); // @audit-qa depositor indexed

File: contracts/liquid-staking/ETHPoolLPFactory.sol
19:     event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount); // @audit-qa token + depositor indexed

File: contracts/liquid-staking/ETHPoolLPFactory.sol
22:     event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount); // @audit-qa token + firstDepositor indexed

File: contracts/liquid-staking/ETHPoolLPFactory.sol
25:     event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount); // @audit-qa token + depositor indexed

File: contracts/liquid-staking/SavETHVault.sol
19:     event DETHRedeemed(address depositor, uint256 amount); // @audit-qa depositor indexed

File: contracts/liquid-staking/SavETHVault.sol
22:     event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount); // @audit-qa NatSpec withdrawalAddress + liquidStakingManager indexed

File: contracts/liquid-staking/StakingFundsVault.sol
25:     event ETHDeposited(address sender, uint256 amount); // @audit-qa indexed sender

File: contracts/liquid-staking/StakingFundsVault.sol
28:     event ETHWithdrawn(address receiver, address admin, uint256 amount); // @audit-qa indexed receiver + admin

File: contracts/liquid-staking/StakingFundsVault.sol
31:     event ERC20Recovered(address admin, address recipient, uint256 amount); // @audit-qa indexed admin + recipient

File: contracts/liquid-staking/StakingFundsVault.sol
34:     event WETHUnwrapped(address admin, uint256 amount);  // @audit-qa indexed admin
```

## Long lines
Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

```bash
File: contracts/liquid-staking/LiquidStakingManager.sol
222:     /// @notice In preparation of a rage quit, restore sETH to a smart wallet which are recoverable with the execution methods in the event this step does not go to plan

File: contracts/syndicate/Syndicate.sol
116:     /// @notice Whether a BLS public key, that has been previously registered, is no longer part of the syndicate and its shares (free floating or SLOT) cannot earn any more rewards

File: contracts/syndicate/Syndicate.sol
119:     /// @notice Once a BLS public key is no longer part of the syndicate, the accumulated ETH per free floating SLOT share is snapshotted so historical earnings can be drawn down correctly

File: contracts/syndicate/Syndicate.sol
398:     /// @notice Preview the amount of unclaimed ETH available for a collatearlized SLOT staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract

File: contracts/syndicate/Syndicate.sol
490:     /// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this amongs the current set of collateralized owners (a dynamic set of addresses and balances)
```

# change function visibility to external if function is not used internally
If a function is not used internally change the visibility to external for a better experience to identify the puprose of the function.

```bash
File: contracts/liquid-staking/GiantPoolBase.sol
34:     function depositETH(uint256 _amount) public payable { // @audit-qa change external

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
29:     function batchDepositETHForStaking(
30:         address[] calldata _savETHVaults,
31:         uint256[] calldata _ETHTransactionAmounts,
32:         bytes[][] calldata _blsPublicKeys,
33:         uint256[][] calldata _stakeAmounts
34:     ) public { // @audit-qa change external

File: contracts/liquid-staking/LiquidStakingManager.sol
514:     function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) { // @audit-qa change external

File: contracts/liquid-staking/SavETHVault.sol
83:     function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) { // @audit-qa change external

File: contracts/liquid-staking/SavETHVault.sol
200:     function withdrawETHForStaking(
201:         address _smartWallet,
202:         uint256 _amount
203:     ) public onlyManager nonReentrant returns (uint256) { // @audit-qa change external

File: contracts/liquid-staking/SavETHVault.sol
218:     function isBLSPublicKeyPartOfLSDNetwork(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) { // @audit-qa change external

File: contracts/liquid-staking/SavETHVault.sol
223:     function isBLSPublicKeyBanned(bytes calldata _blsPublicKeyOfKnot) public view returns (bool) { // @audit-qa change external

File: contracts/liquid-staking/StakingFundsVault.sol
113:     function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) { // @audit-qa change external

File: contracts/liquid-staking/StakingFundsVault.sol
239:     function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) { // @audit-qa change external

File: contracts/syndicate/Syndicate.sol
285:     function claimAsStaker(address _recipient, bytes[] calldata _blsPubKeys) public nonReentrant { // @audit-qa change external

File: contracts/syndicate/Syndicate.sol
458:     function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) { // @audit-qa change external


```

# constants should be defined rather than using magic numbers
It is bad practice to use numbers directly in code without explanation.

```bash
File: contracts/liquid-staking/ETHPoolLPFactory.sol
113:         require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
127:         require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

File: contracts/liquid-staking/LiquidStakingManager.sol
434:         require(msg.value == len * 4 ether, "Insufficient ether provided");

File: contracts/liquid-staking/LiquidStakingManager.sol
751:         savETHVault.withdrawETHForStaking(smartWallet, 24 ether);

File: contracts/liquid-staking/LiquidStakingManager.sol
754:         stakingFundsVault.withdrawETH(smartWallet, 4 ether);

File: contracts/liquid-staking/LiquidStakingManager.sol
768:             32 ether

File: contracts/liquid-staking/LiquidStakingManager.sol
885:         uint256 stakeAmount = 12 ether;

File: contracts/liquid-staking/LiquidStakingManager.sol
939:         require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

File: contracts/liquid-staking/LiquidStakingManager.sol
943:         require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

File: contracts/liquid-staking/LiquidStakingManager.sol
947:         require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");

File: contracts/liquid-staking/SavETHVault.sol
141:         bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;

File: contracts/liquid-staking/StakingFundsVault.sol
58:         totalShares += 4 ether;

File: contracts/liquid-staking/StakingFundsVault.sol
230:             require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");

File: contracts/liquid-staking/StakingFundsVault.sol
380:         maxStakingAmountPerValidator = 4 ether;

File: contracts/syndicate/Syndicate.sol
221:             if (totalStaked == 12 ether) revert KnotIsFullyStakedWithFreeFloatingSlotTokens();

File: contracts/syndicate/Syndicate.sol
223:             if (_sETHAmount + totalStaked > 12 ether) revert InvalidStakeAmount();

File: contracts/syndicate/Syndicate.sol
428: 
429:                 if (currentSlashedAmount < 4 ether) {
430:                     currentAccrued +=
431:                         balance * unprocessedForKnot / (4 ether - currentSlashedAmount);
432:                 }

File: contracts/syndicate/Syndicate.sol
504:             if (currentSlashedAmount < 4 ether) {

File: contracts/syndicate/Syndicate.sol
522:                             balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);


```

# missing checks for address(0) when assigning values to address state variables
It's recommended to check addresses before storing that they're not address(0), except it's wished to be zero.
```bash
File: contracts/liquid-staking/GiantLP.sol
19:     constructor(
20:         address _pool,
21:         address _transferHookProcessor,
22:         string memory _name,
23:         string memory _symbol
24:     ) ERC20(_name, _symbol) {
25:         pool = _pool;


File: contracts/liquid-staking/LPToken.sol
32:     function init(
33:         address _deployer,
34:         address _transferHookProcessor,
35:         string calldata _tokenSymbol,
36:         string calldata _tokenName
37:     ) external override initializer {
38:         deployer = _deployer;

```

# not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

```bash
File: contracts/liquid-staking/LiquidStakingManager.sol
924:     function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {

```

# _ETHTransactionAmounts.length not checked
The length for _ETHTransactionAmounts is not checked in the function.

```bash
File: contracts/liquid-staking/GiantMevAndFeesPool.sol
28:     function batchDepositETHForStaking(
29:         address[] calldata _stakingFundsVault,
30:         uint256[] calldata _ETHTransactionAmounts,
31:         bytes[][] calldata _blsPublicKeyOfKnots,
32:         uint256[][] calldata _amounts
33:     ) external {
34:         uint256 numOfVaults = _stakingFundsVault.length;
35:         require(numOfVaults > 0, "Zero vaults");
36:         require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");
37:         require(numOfVaults == _amounts.length, "Inconsistent lengths");length

```

# no transfer ownership pattern (two-phase ownsership transfer)
Consider implementing a two step process where the owner / admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

contracts/liquid-staking/LiquidStakingManager.sol
```bash
File: contracts/liquid-staking/LiquidStakingManager.sol
239:     function updateDAOAddress(address _newAddress) external onlyDAO {
```

contracts/smart-wallet/OwnableSmartWallet.sol 
```bash
can also move to address(0) and loose complete ownership
```

# use scientific notation (e.g. 1e18) rather than exponentiation (e.g. 10**18)
Use Scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability.

```bash
File: contracts/liquid-staking/LiquidStakingManager.sol
158:     uint256 public MODULO = 100_00000;

```

# add missing documentation
The contracts are very well documented and clear to read. Nearly every public function in the contracts contract have complete NatSpec comments.
Still it misses some documentation that could be added for a even better developer experience.

Functions missing @param (or wrong param mentioned) / @return / @notice or is missing complete
```bash
File: contracts/liquid-staking/GiantLP.sol
29:     function mint(address _recipient, uint256 _amount) external { // @audit-qa NatSpec

File: contracts/liquid-staking/GiantLP.sol
34:     function burn(address _recipient, uint256 _amount) external { // @audit-qa NatSpec

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
56:     function claimRewards( // @audit-qa NatSpec

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
82:     function previewAccumulatedETH( // @audit-qa NatSpec

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
105:     function batchRotateLPTokens( // @audit-qa NatSpec param 2 x _oldLPTokens

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
146:     function beforeTokenTransfer(address _from, address _to, uint256) external { // @audit-qa NatSpec

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
170:     function afterTokenTransfer(address, address _to, uint256) external { // @audit-qa NatSpec

File: contracts/liquid-staking/GiantMevAndFeesPool.sol
176:     function totalRewardsReceived() public view override returns (uint256) { // @audit-qa NatSpec

File: contracts/liquid-staking/GiantPoolBase.sol
34:     function depositETH(uint256 _amount) public payable { // @audit-qa NatSpec

File: contracts/liquid-staking/GiantSavETHVaultPool.sol
116:     function batchRotateLPTokens( // @audit-qa NatSpec param 2 x _oldLPTokens

File: contracts/liquid-staking/LPToken.sol
32:     function init(
33:         address _deployer,
34:         address _transferHookProcessor,
35:         string calldata _tokenSymbol,
36:         string calldata _tokenName
37:     ) external override initializer { // @audit-qa NatSpec

File: contracts/liquid-staking/LPToken.sol
46:     function mint(address _recipient, uint256 _amount) external onlyDeployer { // @audit-qa NatSpec

File: contracts/liquid-staking/LPToken.sol
51:     function burn(address _recipient, uint256 _amount) external onlyDeployer { // @audit-qa NatSpec

File: contracts/liquid-staking/LPToken.sol
56:     function liquidStakingManager() external view returns (address) { // @audit-qa NatSpec

File: contracts/liquid-staking/LPTokenFactory.sol
27:     function deployLPToken(
28:         address _deployer,
29:         address _transferHookProcessor,
30:         string calldata _tokenSymbol,
31:         string calldata _tokenName
32:     ) external returns (address) { // @audit-qa NatSpec

File: contracts/liquid-staking/LSDNFactory.sol
73:     function deployNewLiquidStakingDerivativeNetwork( // @audit-qa NatSpec

File: contracts/liquid-staking/LiquidStakingManager.sol
218:     function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO { // @audit-qa NatSpec

File: contracts/liquid-staking/LiquidStakingManager.sol
239:     function updateDAOAddress(address _newAddress) external onlyDAO { // @audit-qa NatSpec

File: contracts/liquid-staking/LiquidStakingManager.sol
249:     function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO { // @audit-qa NatSpec

File: contracts/liquid-staking/LiquidStakingManager.sol
255:     function updateTicker(string calldata _newTicker) external onlyDAO { // @audit-qa NatSpec

File: contracts/liquid-staking/LiquidStakingManager.sol
267:     function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) { // @audit-qa NatSpec

File: contracts/liquid-staking/LiquidStakingManager.sol
326:     function withdrawETHForKnot(address _recipient, bytes calldata _blsPublicKeyOfKnot) external { // @audit-qa NatSpec

File: contracts/liquid-staking/LiquidStakingManager.sol
356:     function rotateNodeRunnerOfSmartWallet(address _current, address _new, bool _wasPreviousNodeRunnerMalicious) external { // @audit-qa NatSpec

File: contracts/liquid-staking/LiquidStakingManager.sol
635:     function getNetworkFeeRecipient() external view returns (address) { // @audit-qa NatSpec

File: contracts/liquid-staking/OptionalGatekeeperFactory.sol
11:     function deploy(address _liquidStakingManager) external returns (OptionalHouseGatekeeper) { // @audit-qa NatSpec

File: contracts/liquid-staking/OptionalHouseGatekeeper.sol
19:     function isMemberPermitted(bytes calldata _blsPublicKeyOfKnot) external override view returns (bool) { // @audit-qa NatSpec

File: contracts/liquid-staking/SavETHVault.sol
22:     event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount); // @audit-qa NatSpec

File: contracts/liquid-staking/SavETHVault.sol
45:     function init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) external virtual initializer { // @audit-qa NatSpec

File: contracts/liquid-staking/SavETHVault.sol
218:     function isBLSPublicKeyPartOfLSDNetwork(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) { // @audit-qa NatSpec

File: contracts/liquid-staking/SavETHVault.sol
223:     function isBLSPublicKeyBanned(bytes calldata _blsPublicKeyOfKnot) public view returns (bool) { // @audit-qa NatSpec

File: contracts/liquid-staking/SavETHVault.sol
228:     function isDETHReadyForWithdrawal(address _lpTokenAddress) external view returns (bool) { // @audit-qa NatSpec

File: contracts/liquid-staking/SavETHVaultDeployer.sol
18:     function deploySavETHVault(address _liquidStakingManger, address _lpTokenFactory) external returns (address) { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVault.sol
46:     function init(address _liquidStakingNetworkManager, LPTokenFactory _lpTokenFactory) external virtual initializer { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVault.sol
113:     function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVault.sol
199:     function claimRewards(
200:         address _recipient,
201:         bytes[] calldata _blsPubKeys
202:     ) external nonReentrant { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVault.sol
255:     function unstakeSyndicateSharesForRageQuit(
256:         address _sETHRecipient,
257:         bytes[] calldata _blsPublicKeys,
258:         uint256[] calldata _amounts
259:     ) external onlyManager nonReentrant { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVault.sol
274:     function batchPreviewAccumulatedETHByBLSKeys(address _user, bytes[] calldata _blsPubKeys) external view returns (uint256) { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVault.sol
284:     function batchPreviewAccumulatedETH(address _user, LPToken[] calldata _token) external view returns (uint256) { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVault.sol
293:     function previewAccumulatedETH(address _user, LPToken _token) public view returns (uint256) { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVault.sol
315:     function beforeTokenTransfer(address _from, address _to, uint256) external override { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVault.sol
343:     function afterTokenTransfer(address, address _to, uint256) external override { // @audit-qa NatSpec

File: contracts/liquid-staking/StakingFundsVaultDeployer.sol
18:     function deployStakingFundsVault(address _liquidStakingManager, address _tokenFactory) external returns (address) { // @audit-qa NatSpec

File: contracts/liquid-staking/SyndicateRewardsProcessor.sol
93:     function totalRewardsReceived() public virtual view returns (uint256) { // @audit-qa NatSpec

File: contracts/smart-wallet/OwnableSmartWalletFactory.sol
28:     function createWallet() external returns (address wallet) { // @audit-qa NatSpec

File: contracts/smart-wallet/OwnableSmartWalletFactory.sol
32:     function createWallet(address owner) external returns (address wallet) { // @audit-qa NatSpec
```

# typos
There are some typos in the comments.

```bash
--- a/contracts/liquid-staking/ETHPoolLPFactory.sol
+++ b/contracts/liquid-staking/ETHPoolLPFactory.sol
@@ -71,7 +71,7 @@ abstract contract ETHPoolLPFactory is StakehouseAPI {

     /// @notice Allow users to rotate the ETH from one LP token to another in the event that the BLS key is never staked
     /// @param _oldLPToken Instance of the old LP token (to be burnt)
-    /// @param _newLPToken Instane of the new LP token (to be minted)
+    /// @param _newLPToken Instance of the new LP token (to be minted)
     /// @param _amount Amount of LP tokens to be rotated/converted from old to new
     function rotateLPTokens(LPToken _oldLPToken, LPToken _newLPToken, uint256 _amount) public {
         require(address(_oldLPToken) != address(0), "Zero address");
@@ -121,7 +121,7 @@ abstract contract ETHPoolLPFactory is StakehouseAPI {
             // total supply after minting the LP token must not exceed maximum staking amount per validator
             require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");

-            // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied
+            // mint LP tokens for the depositor with 1:1 ratio of LP tokens and ETH supplied
             lpToken.mint(msg.sender, _amount);
             emit LPTokenMinted(_blsPublicKeyOfKnot, address(lpToken), msg.sender, _amount);
         }
@@ -147,7 +147,7 @@ abstract contract ETHPoolLPFactory is StakehouseAPI {
             lpTokenForKnot[_blsPublicKeyOfKnot] = newLPToken;
             KnotAssociatedWithLPToken[newLPToken] = _blsPublicKeyOfKnot;

-            // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied
+            // mint LP tokens for the depositor with 1:1 ratio of LP tokens and ETH supplied
             newLPToken.mint(msg.sender, _amount);
             emit NewLPTokenIssued(_blsPublicKeyOfKnot, address(newLPToken), msg.sender, _amount);
         }

--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -98,7 +98,7 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
     /// @notice address of the DAO deploying the contract
     address public dao;

-    /// @notice address of optional gatekeeper for admiting new knots to the house created by the network
+    /// @notice address of optional gatekeeper for admitting new knots to the house created by the network
     OptionalHouseGatekeeper public gatekeeper;

     /// @notice instance of the syndicate factory that deploys the syndicates
@@ -473,7 +473,7 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
                 "Lifecycle status must be zero"
             );

-            // register validtor initals for each of the KNOTs
+            // register validator initals for each of the KNOTs

--- a/contracts/liquid-staking/SavETHVault.sol
+++ b/contracts/liquid-staking/SavETHVault.sol
@@ -112,7 +112,7 @@ contract SavETHVault is Initializable, ETHPoolLPFactory, ReentrancyGuard {
     function burnLPTokens(LPToken[] calldata _lpTokens, uint256[] calldata _amounts) external {
         uint256 numOfTokens = _lpTokens.length;
         require(numOfTokens > 0, "Empty arrays");
-        require(numOfTokens == _amounts.length, "Inconsisent array length");
+        require(numOfTokens == _amounts.length, "Inconsistent array length");

--- a/contracts/smart-wallet/OwnableSmartWallet.sol
+++ b/contracts/smart-wallet/OwnableSmartWallet.sol
@@ -8,9 +8,9 @@ import {Initializable} from "@openzeppelin/contracts/proxy/utils/Initializable.s
 import {Address} from "@openzeppelin/contracts/utils/Address.sol";

 /// @title Ownable smart wallet
-/// @notice Ownable and transferrable smart wallet that allows the owner to
+/// @notice Ownable and transferable smart wallet that allows the owner to
 ///         interact with any contracts the same way as from an EOA. The
-///         main intended use is to make non-transferrable positions and assets
+///         main intended use is to make non-transferable positions and assets

--- a/contracts/syndicate/Syndicate.sol
+++ b/contracts/syndicate/Syndicate.sol
@@ -279,7 +279,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
         }
     }

-    /// @notice Claim ETH cashflow from the syndicate as an sETH staker proportional to how much the user has staked
+    /// @notice Claim ETH cash flow from the syndicate as an sETH staker proportional to how much the user has staked
     /// @param _recipient Address that will receive the share of ETH funds
     /// @param _blsPubKeys List of BLS public keys that the caller has staked against
     function claimAsStaker(address _recipient, bytes[] calldata _blsPubKeys) public nonReentrant {
@@ -395,7 +395,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
         return userShare - sETHUserClaimForKnot[_blsPubKey][_staker];
     }

-    /// @notice Preview the amount of unclaimed ETH available for a collatearlized SLOT staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract
+    /// @notice Preview the amount of unclaimed ETH available for a collateralized SLOT staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract
     /// @param _staker Address of a collateralized SLOT owner for a KNOT
     /// @param _blsPubKey BLS public key of the KNOT that is registered with the syndicate
     function previewUnclaimedETHAsCollateralizedSlotOwner(
@@ -487,7 +487,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S
         emit ContractDeployed();
     }

-    /// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this amongs the current set of collateralized owners (a dynamic set of addresses and balances)
+    /// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this among the current set of collateralized owners (a dynamic set of addresses and balances)
     function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {
         // Establish how much new ETH is for the new KNOT
         uint256 unprocessedETHForCurrentKnot =
@@ -651,7 +651,7 @@ contract Syndicate is ISyndicateInit, Initializable, Ownable, ReentrancyGuard, S

             uint256 unclaimedUserShare = calculateUnclaimedFreeFloatingETHShare(_blsPubKey, msg.sender);

-            // this means that user can call the funtion even if there is nothing to claim but the
+            // this means that user can call the function even if there is nothing to claim but the
```

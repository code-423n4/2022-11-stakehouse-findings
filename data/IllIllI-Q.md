## Summary



### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Unused/empty `receive()`/`fallback()` function | 2 |
| [L&#x2011;02] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 3 |
| [L&#x2011;03] | Open TODOs | 1 |

Total: 6 instances over 3 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 1 |
| [N&#x2011;02] | Duplicate import statements | 1 |
| [N&#x2011;03] | The `nonReentrant` `modifier` should occur before all other modifiers | 3 |
| [N&#x2011;04] | `public` functions not called by the contract should be declared `external` instead | 10 |
| [N&#x2011;05] | `2**<n> - 1` should be re-written as `type(uint<n>).max` | 1 |
| [N&#x2011;06] | `constant`s should be defined rather than using magic numbers | 39 |
| [N&#x2011;07] | Missing event and or timelock for critical parameter change | 1 |
| [N&#x2011;08] | Constant redefined elsewhere | 2 |
| [N&#x2011;09] | Inconsistent spacing in comments | 1 |
| [N&#x2011;10] | Lines are too long | 5 |
| [N&#x2011;11] | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 1 |
| [N&#x2011;12] | Non-library/interface files should use fixed compiler versions, not floating ones | 16 |
| [N&#x2011;13] | File is missing NatSpec | 4 |
| [N&#x2011;14] | NatSpec is incomplete | 20 |
| [N&#x2011;15] | Event is missing `indexed` fields | 35 |
| [N&#x2011;16] | Not using the named return variables anywhere in the function is confusing | 2 |
| [N&#x2011;17] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 19 |

Total: 161 instances over 17 issues




## Low Risk Issues

### [L&#x2011;01]  Unused/empty `receive()`/`fallback()` function
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. `require(msg.sender == address(weth))`). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

*There are 2 instances of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

620:      receive() external payable {}

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L620

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

98:       receive() external payable {}

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98

### [L&#x2011;02]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 3 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantLP.sol

25:           pool = _pool;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantLP.sol#L25

```solidity
File: contracts/liquid-staking/LPToken.sol

38:           deployer = _deployer;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPToken.sol#L38

```solidity
File: contracts/syndicate/SyndicateFactory.sol

17:           syndicateImplementation = _syndicateImpl;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/SyndicateFactory.sol#L17

### [L&#x2011;03]  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There is 1 instance of this issue:*
```solidity
File: contracts/syndicate/Syndicate.sol

195:              // todo - check else case for any ETH lost

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L195

## Non-critical Issues

### [N&#x2011;01]  Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*There is 1 instance of this issue:*
```solidity
File: contracts/liquid-staking/LPToken.sol

11:   contract LPToken is ILPTokenInit, ILiquidStakingManagerChildContract, Initializable, ERC20PermitUpgradeable {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPToken.sol#L11

### [N&#x2011;02]  Duplicate import statements

*There is 1 instance of this issue:*
```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

11:   import { LPToken } from "./LPToken.sol";

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L11

### [N&#x2011;03]  The `nonReentrant` `modifier` should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

*There are 3 instances of this issue:*
```solidity
File: contracts/liquid-staking/SavETHVault.sol

203:      ) public onlyManager nonReentrant returns (uint256) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L203

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

239:      function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {

259:      ) external onlyManager nonReentrant {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L239

### [N&#x2011;04]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 10 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

34:       function depositETH(uint256 _amount) public payable {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L34

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

29        function batchDepositETHForStaking(
30            address[] calldata _savETHVaults,
31            uint256[] calldata _ETHTransactionAmounts,
32            bytes[][] calldata _blsPublicKeys,
33:           uint256[][] calldata _stakeAmounts

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L29-L33

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

73        function deployNewLiquidStakingDerivativeNetwork(
74            address _dao,
75            uint256 _optionalCommission,
76            bool _deployOptionalHouseGatekeeper,
77            string calldata _stakehouseTicker
78:       ) public returns (address) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LSDNFactory.sol#L73-L78

```solidity
File: contracts/liquid-staking/SavETHVault.sol

83:       function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) {

200       function withdrawETHForStaking(
201           address _smartWallet,
202           uint256 _amount
203:      ) public onlyManager nonReentrant returns (uint256) {

223:      function isBLSPublicKeyBanned(bytes calldata _blsPublicKeyOfKnot) public view returns (bool) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L83

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

113:      function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) {

239:      function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L113

```solidity
File: contracts/syndicate/Syndicate.sol

285:      function claimAsStaker(address _recipient, bytes[] calldata _blsPubKeys) public nonReentrant {

458:      function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L285

### [N&#x2011;05]  `2**<n> - 1` should be re-written as `type(uint<n>).max`
Earlier versions of solidity can use `uint<n>(-1)` instead. Expressions not including the `- 1` can often be re-written to accomodate the change (e.g. by using a `>` rather than a `>=`, which will also save some gas)

*There is 1 instance of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

861:          sETH.approve(syndicate, (2 ** 256) - 1);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L861

### [N&#x2011;06]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 39 instances of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

/// @audit 30
82:           require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");

/// @audit 24
83:           require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

/// @audit 48
88:           require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");

/// @audit 48
89:           require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");

/// @audit 48
112:          require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L82

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

/// @audit 0.5
116:          require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L116

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

/// @audit 0.5
127:          require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L127

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit 3
256:          require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

/// @audit 5
257:          require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

/// @audit 4
333:          require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");

/// @audit 4
343:              4 ether

/// @audit 4
432:          require(msg.value == len * 4 ether, "Insufficient ether provided");

/// @audit 3
653:          require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");

/// @audit 5
654:          require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");

/// @audit 24
740:          savETHVault.withdrawETHForStaking(smartWallet, 24 ether);

/// @audit 4
743:          stakingFundsVault.withdrawETH(smartWallet, 4 ether);

/// @audit 32
757:              32 ether

/// @audit 256
861:          sETH.approve(syndicate, (2 ** 256) - 1);

/// @audit 12
873:          uint256 stakeAmount = 12 ether;

/// @audit 4
927:          require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");

/// @audit 4
931:          require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");

/// @audit 24
935:          require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L256

```solidity
File: contracts/liquid-staking/SavETHVault.sol

/// @audit 30
141:          bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;

/// @audit 24
161:                  require(dETHBalance >= 24 ether, "Nothing to withdraw");

/// @audit 24
174:              redemptionValue = (dETHDetails.savETHBalance * _amount) / 24 ether;

/// @audit 24
204:          require(_amount >= 24 ether, "Amount cannot be less than 24 ether");

/// @audit 24
244:          maxStakingAmountPerValidator = 24 ether;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L141

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

/// @audit 4
58:           totalShares += 4 ether;

/// @audit 30
184:          require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");

/// @audit 30
230:              require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");

/// @audit 4
240:          require(_amount >= 4 ether, "Amount cannot be less than 4 ether");

/// @audit 4
380:          maxStakingAmountPerValidator = 4 ether;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L58

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit 12
221:              if (totalStaked == 12 ether) revert KnotIsFullyStakedWithFreeFloatingSlotTokens();

/// @audit 12
223:              if (_sETHAmount + totalStaked > 12 ether) revert InvalidStakeAmount();

/// @audit 4
429:                  if (currentSlashedAmount < 4 ether) {

/// @audit 4
431:                          balance * unprocessedForKnot / (4 ether - currentSlashedAmount);

/// @audit 4
504:              if (currentSlashedAmount < 4 ether) {

/// @audit 4
522:                              balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);

/// @audit 4
546:          return (_ethSinceLastUpdate * PRECISION) / (numberOfRegisteredKnots * 4 ether);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L221

### [N&#x2011;07]  Missing event and or timelock for critical parameter change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

*There is 1 instance of this issue:*
```solidity
File: contracts/syndicate/Syndicate.sol

168       function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
169           updateAccruedETHPerShares();
170           priorityStakingEndBlock = _endBlock;
171:      }

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L168-L171

### [N&#x2011;08]  Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

*There are 2 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

/// @audit seen in contracts/liquid-staking/ETHPoolLPFactory.sol 
22:       uint256 public constant MIN_STAKING_AMOUNT = 0.001 ether;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L22

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit seen in contracts/liquid-staking/SyndicateRewardsProcessor.sol 
66:       uint256 public constant PRECISION = 1e24;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L66

### [N&#x2011;09]  Inconsistent spacing in comments
Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the majority, within each file

*There is 1 instance of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

59:       ///@notice signalize removal of representative from smart wallet

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L59

### [N&#x2011;10]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 5 instances of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

222:      /// @notice In preparation of a rage quit, restore sETH to a smart wallet which are recoverable with the execution methods in the event this step does not go to plan

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L222

```solidity
File: contracts/syndicate/Syndicate.sol

116:      /// @notice Whether a BLS public key, that has been previously registered, is no longer part of the syndicate and its shares (free floating or SLOT) cannot earn any more rewards

119:      /// @notice Once a BLS public key is no longer part of the syndicate, the accumulated ETH per free floating SLOT share is snapshotted so historical earnings can be drawn down correctly

398:      /// @notice Preview the amount of unclaimed ETH available for a collatearlized SLOT staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract

490:      /// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this amongs the current set of collateralized owners (a dynamic set of addresses and balances)

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L116

### [N&#x2011;11]  Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` _function_ should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*There is 1 instance of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

158:      uint256 public MODULO = 100_00000;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L158

### [N&#x2011;12]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 16 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantLP.sol

1:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantLP.sol#L1

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

1:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L1

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

1:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L1

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

1:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L1

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

3:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L3

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

1:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPTokenFactory.sol#L1

```solidity
File: contracts/liquid-staking/LPToken.sol

1:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPToken.sol#L1

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

1:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LSDNFactory.sol#L1

```solidity
File: contracts/liquid-staking/OptionalGatekeeperFactory.sol

3:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L3

```solidity
File: contracts/liquid-staking/OptionalHouseGatekeeper.sol

1:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L1

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol

1:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVaultDeployer.sol#L1

```solidity
File: contracts/liquid-staking/SavETHVault.sol

3:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L3

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol

3:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L3

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

3:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L3

```solidity
File: contracts/smart-wallet/OwnableSmartWalletFactory.sol

3:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L3

```solidity
File: contracts/smart-wallet/OwnableSmartWallet.sol

3:    pragma solidity ^0.8.13;

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/smart-wallet/OwnableSmartWallet.sol#L3

### [N&#x2011;13]  File is missing NatSpec

*There are 4 instances of this issue:*
```solidity
File: contracts/liquid-staking/OptionalGatekeeperFactory.sol


```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/OptionalGatekeeperFactory.sol

```solidity
File: contracts/liquid-staking/SavETHVaultDeployer.sol


```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVaultDeployer.sol

```solidity
File: contracts/liquid-staking/StakingFundsVaultDeployer.sol


```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVaultDeployer.sol

```solidity
File: contracts/syndicate/SyndicateErrors.sol


```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/SyndicateErrors.sol

### [N&#x2011;14]  NatSpec is incomplete

*There are 20 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

/// @audit Missing: '@param _newLPTokens'
100       /// @notice Any ETH supplied to a BLS key registered with a liquid staking network can be rotated to another key if it never gets staked
101       /// @param _stakingFundsVaults List of staking funds vaults this contract will contact
102       /// @param _oldLPTokens List of savETH vault LP tokens that the vault has
103       /// @param _oldLPTokens List of new savETH vault LP tokens that the vault wants to receive in exchange for moving ETH to a new KNOT
104       /// @param _amounts Amount of old being swapped for new per LP token
105       function batchRotateLPTokens(
106           address[] calldata _stakingFundsVaults,
107           LPToken[][] calldata _oldLPTokens,
108           LPToken[][] calldata _newLPTokens,
109:          uint256[][] calldata _amounts

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L100-L109

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

/// @audit Missing: '@param _newLPTokens'
111       /// @notice Any ETH supplied to a BLS key registered with a liquid staking network can be rotated to another key if it never gets staked
112       /// @param _savETHVaults List of savETH vaults this contract will contact
113       /// @param _oldLPTokens List of savETH vault LP tokens that the vault has
114       /// @param _oldLPTokens List of new savETH vault LP tokens that the vault wants to receive in exchange for moving ETH to a new KNOT
115       /// @param _amounts Amount of old being swapped for new per LP token
116       function batchRotateLPTokens(
117           address[] calldata _savETHVaults,
118           LPToken[][] calldata _oldLPTokens,
119           LPToken[][] calldata _newLPTokens,
120:          uint256[][] calldata _amounts

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L111-L120

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit Missing: '@return'
265       /// @notice function to change whether node runner whitelisting of node runners is required by the DAO
266       /// @param _changeWhitelist boolean value. true to enable and false to disable
267:      function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {

/// @audit Missing: '@param _recipient'
323       /// @notice Allow node runners to withdraw ETH from their smart wallet. ETH can only be withdrawn until the KNOT has not been staked.
324       /// @dev A banned node runner cannot withdraw ETH for the KNOT. 
325       /// @param _blsPublicKeyOfKnot BLS public key of the KNOT for which the ETH needs to be withdrawn
326:      function withdrawETHForKnot(address _recipient, bytes calldata _blsPublicKeyOfKnot) external {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L265-L267

```solidity
File: contracts/liquid-staking/LPTokenFactory.sol

/// @audit Missing: '@param _deployer'
/// @audit Missing: '@param _transferHookProcessor'
/// @audit Missing: '@return'
24        /// @notice Deploys a new LP token
25        /// @param _tokenSymbol Symbol of the LP token to be deployed
26        /// @param _tokenName Name of the LP token to be deployed
27        function deployLPToken(
28            address _deployer,
29            address _transferHookProcessor,
30            string calldata _tokenSymbol,
31            string calldata _tokenName
32:       ) external returns (address) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPTokenFactory.sol#L24-L32

```solidity
File: contracts/liquid-staking/LPToken.sol

/// @audit Missing: '@param _tokenSymbol'
/// @audit Missing: '@param _tokenName'
30        /// @param _deployer Address of the account deploying the LP token
31        /// @param _transferHookProcessor Optional contract account that can be notified about transfer hooks
32        function init(
33            address _deployer,
34            address _transferHookProcessor,
35            string calldata _tokenSymbol,
36            string calldata _tokenName
37:       ) external override initializer {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LPToken.sol#L30-L37

```solidity
File: contracts/liquid-staking/LSDNFactory.sol

/// @audit Missing: '@param _optionalCommission'
/// @audit Missing: '@param _deployOptionalHouseGatekeeper'
/// @audit Missing: '@return'
70        /// @notice Deploys a new LSDN and the liquid staking manger required to manage the network
71        /// @param _dao Address of the entity that will govern the liquid staking network
72        /// @param _stakehouseTicker Liquid staking derivative network ticker (between 3-5 chars)
73        function deployNewLiquidStakingDerivativeNetwork(
74            address _dao,
75            uint256 _optionalCommission,
76            bool _deployOptionalHouseGatekeeper,
77            string calldata _stakehouseTicker
78:       ) public returns (address) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LSDNFactory.sol#L70-L78

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

/// @audit Missing: '@param _lpTokenFactory'
45        /// @param _liquidStakingNetworkManager address of the liquid staking network manager
46:       function init(address _liquidStakingNetworkManager, LPTokenFactory _lpTokenFactory) external virtual initializer {

/// @audit Missing: '@return'
111       /// @param _blsPublicKeyOfKnot BLS public key of validator registered by a node runner
112       /// @param _amount Amount of ETH being staked
113:      function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) {

/// @audit Missing: '@param _recipient'
197       /// @notice Any LP tokens for BLS keys that have had their derivatives minted can claim ETH from the syndicate contract
198       /// @param _blsPubKeys List of BLS public keys being processed
199       function claimRewards(
200           address _recipient,
201           bytes[] calldata _blsPubKeys
202:      ) external nonReentrant {

/// @audit Missing: '@param _sETHRecipient'
252       /// @notice For any knots that are no longer part of syndicate facilitate unstaking so that knot can rage quit
253       /// @param _blsPublicKeys List of BLS public keys being processed (assuming DAO only has BLS pub keys from correct smart wallet)
254       /// @param _amounts Amounts of free floating sETH that will be unstaked
255       function unstakeSyndicateSharesForRageQuit(
256           address _sETHRecipient,
257           bytes[] calldata _blsPublicKeys,
258           uint256[] calldata _amounts
259:      ) external onlyManager nonReentrant {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L45-L46

```solidity
File: contracts/syndicate/Syndicate.sol

/// @audit Missing: '@param _recipient'
289       /// @param _blsPubKeys List of BLS public keys that the caller has staked against
290       function claimAsCollateralizedSLOTOwner(
291           address _recipient,
292           bytes[] calldata _blsPubKeys
293:      ) external nonReentrant {

/// @audit Missing: '@return'
357       /// @param _blsPubKey BLS public key of the KNOT that is registered with the syndicate
358       /// @param _user The address of a user that has staked sETH against the BLS public key
359:      function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {

/// @audit Missing: '@return'
382       /// @param _blsPubKey BLS public key of the KNOT that is registered with the syndicate
383       /// @param _staker The address of a user that has staked sETH against the BLS public key
384       function previewUnclaimedETHAsFreeFloatingStaker(
385           address _staker,
386           bytes calldata _blsPubKey
387:      ) external view returns (uint256) {

/// @audit Missing: '@return'
399       /// @param _staker Address of a collateralized SLOT owner for a KNOT
400       /// @param _blsPubKey BLS public key of the KNOT that is registered with the syndicate
401       function previewUnclaimedETHAsCollateralizedSlotOwner(
402           address _staker,
403           bytes calldata _blsPubKey
404:      ) external view returns (uint256) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L289-L293

### [N&#x2011;15]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 35 instances of this issue:*
```solidity
File: contracts/liquid-staking/ETHPoolLPFactory.sol

16:       event ETHWithdrawnByDepositor(address depositor, uint256 amount);

19:       event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

22:       event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

25:       event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/ETHPoolLPFactory.sol#L16

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

13:       event ETHDeposited(address indexed sender, uint256 amount);

16:       event LPBurnedForETH(address indexed sender, uint256 amount);

19:       event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L13

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

16:       event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L16

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

36:       event WhitelistingStatusChanged(address indexed dao, bool updatedStatus);

39:       event NodeRunnerWhitelistingStatusChanged(address indexed nodeRunner, bool updatedStatus);

48:       event WalletCredited(address indexed smartWallet, uint256 amount);

51:       event KnotStaked(bytes _blsPublicKeyOfKnot, address indexed trigerringAddress);

54:       event StakehouseCreated(string stakehouseTicker, address indexed stakehouse);

57:       event StakehouseJoined(bytes blsPubKey);

63:       event DormantRepresentative(address indexed associatedSmartWallet, address representative);

66:       event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);

69:       event NetworkTickerUpdated(string newTicker);

84:       event DAOCommissionUpdated(uint256 old, uint256 newCommission);

87:       event NewLSDValidatorRegistered(address indexed nodeRunner, bytes blsPublicKey);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L36

```solidity
File: contracts/liquid-staking/SavETHVault.sol

19:       event DETHRedeemed(address depositor, uint256 amount);

22:       event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

121:      event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L19

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

25:       event ETHDeposited(address sender, uint256 amount);

28:       event ETHWithdrawn(address receiver, address admin, uint256 amount);

31:       event ERC20Recovered(address admin, address recipient, uint256 amount);

34:       event WETHUnwrapped(address admin, uint256 amount);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L25

```solidity
File: contracts/liquid-staking/SyndicateRewardsProcessor.sol

9:        event ETHReceived(uint256 amount);

12:       event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L9

```solidity
File: contracts/syndicate/Syndicate.sol

42:       event UpdateAccruedETH(uint256 unprocessed);

45:       event CollateralizedSLOTReCalibrated(bytes BLSPubKey);

48:       event KNOTRegistered(bytes BLSPubKey);

51:       event KnotDeRegistered(bytes BLSPubKey);

57:       event Staked(bytes BLSPubKey, uint256 amount);

60:       event UnStaked(bytes BLSPubKey, uint256 amount);

63:       event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/syndicate/Syndicate.sol#L42

### [N&#x2011;16]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 2 instances of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit _nodeRunner
/// @audit _dao
912:      function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L912

### [N&#x2011;17]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There are 19 instances of this issue:*
```solidity
File: contracts/liquid-staking/GiantLP.sol

35:           require(msg.sender == pool, "Only pool");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantLP.sol#L35

```solidity
File: contracts/liquid-staking/GiantMevAndFeesPool.sol

171:          require(msg.sender == address(lpTokenETH), "Caller is not giant LP");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantMevAndFeesPool.sol#L171

```solidity
File: contracts/liquid-staking/GiantPoolBase.sol

94:           require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantPoolBase.sol#L94

```solidity
File: contracts/liquid-staking/GiantSavETHVaultPool.sol

143:          require(numOfVaults > 0, "Empty arrays");

144:          require(numOfVaults == _lpTokens.length, "Inconsistent arrays");

145:          require(numOfVaults == _amounts.length, "Inconsistent arrays");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/GiantSavETHVaultPool.sol#L143

```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

676:          require(_nodeRunner != address(0), "Zero address");

309:          require(_newRepresentative != address(0), "Zero address");

435:          require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

312:          require(smartWallet != address(0), "No smart wallet");

313:          require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");

314:          require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

385:          require(_recipient != address(0), "Zero address");

414:              require(transferResult, "Failed to transfer");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L676

```solidity
File: contracts/liquid-staking/SavETHVault.sol

114:          require(numOfTokens > 0, "Empty arrays");

210:          require(result, "Transfer failed");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/SavETHVault.sol#L114

```solidity
File: contracts/liquid-staking/StakingFundsVault.sol

164:          require(numOfTokens > 0, "Empty arrays");

165:          require(numOfTokens == _amounts.length, "Inconsistent array length");

245:          require(result, "Transfer failed");

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/StakingFundsVault.sol#L164




___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Return values of `approve()` not checked | 1 |

Total: 1 instances over 1 issues



## Non-critical Issues

### [N&#x2011;01]  Return values of `approve()` not checked
Not all `IERC20` implementations `revert()` when there's a failure in `approve()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything

*There is 1 instance of this issue:*
```solidity
File: contracts/liquid-staking/LiquidStakingManager.sol

/// @audit (valid but excluded finding)
861:          sETH.approve(syndicate, (2 ** 256) - 1);

```
https://github.com/code-423n4/2022-11-stakehouse/blob/fac28671afb64b065fc7ffd10d730fe20264bc31/contracts/liquid-staking/LiquidStakingManager.sol#L861



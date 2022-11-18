## Summary

### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Draft Openzeppelin Dependencies| 1 |
|[L-02]| Stack too deep when compiling |  |
|[L-03]| Remove unused code | 2 |
|[L-04]|Insufficient coverage |  |
|[L-05]|Critical Address Changes Should Use Two-step Procedure |  |
|[L-06]|Avoid variable names that can shade  | 1 |
|[L-07]|Use a more recent version of Solidity | All contracts |
|[L-08]| Owner can renounce Ownership | 2 |
|[L-09]| _Lock pragmas_ to specific compiler version | 24 |
|[L-10]| Loss of precision due to rounding| 1 |
|[L-11]| Using vulnerable dependency of OpenZeppelin | 1 |
|[L-12]|Use ```safeTransferOwnership``` instead of ```transferOwnership``` function | 2 |

Total 12 issues

### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [NC-01]|`0 address` check |7|
| [NC-02] |Add parameter to Event-Emit  |1|
| [NC-03] | Omissions in Events | 1 |
| [NC-04] | Include ``return parameters`` in _NatSpec comments_| All contracts |
| [NC-05] | Use a more recent version of Solidity | All contracts|
| [NC-06] |Solidity compiler optimizations can be problematic  |  |
| [NC-07] |NatSpec is missing | 27 |
| [NC-08] |Lines are too long | 9|
| [NC-09] |Missing Event for critical parameters change| 1 |
| [NC-10] | Add to indexed parameter for countable Events | 4 |
| [NC-11] |NatSpec comments should be increased in contracts | All contracts |
| [NC-12] |Open TODOs | 1 |
| [NC-13] |`Empty blocks` should be _removed_ or _Emit_ something | 10 |

Total 13 issues


### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Generate perfect code headers every time |

Total 1 suggestion

### [L-01] Draft Openzeppelin Dependencies

The `LPToken.sol` contract utilised draft-ERC20PermitUpgradeable.sol , an OpenZeppelin contract. This contract is still a draft and is not considered ready for mainnet use. OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

[LPToken.sol#L6](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L6)

```solidity
contracts/liquid-staking/LPToken.sol:

  6: import { ERC20PermitUpgradeable } from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-ERC20PermitUpgradeable.sol";
```

### [L-02] Stack too deep when compiling

The project cannot be compiled due to the "stack too deep" error.

The “stack too deep” error is a limitation of the current code generator. The EVM stack only has 16 slots and that’s sometimes not enough to fit all the local variables, parameters and/or return variables. The solution is to move some of them to memory, which is more expensive but at least makes your code compile.

```js
[⠒] Compiling...
[⠰] Compiling 100 files with 0.8.13
[⠔] Solc 0.8.13 finished in 3.35s
Error: 
Compiler run failed
CompilerError: Stack too deep when compiling inline assembly: Variable headStart is 1 slot(s) too deep inside the stack.
```

ref:https://forum.openzeppelin.com/t/stack-too-deep-when-compiling-inline-assembly/11391/6

### [L-03] Remove unused code

This code is not used in the project, remove it or add event-emit;

```solidity
contracts/liquid-staking/GiantPoolBase.sol:

  100:     function _onDepositETH() internal virtual {}
  103:     function _onWithdraw(LPToken[] calldata _lpTokens) internal virtual {}
  104  }
```
### [L-04] Insufficient coverage

**Description:**
Testing all functions is best practice in terms of security criteria.

This function test coverage is not found in test files

```solidity
 function rawExecute(
        address target,
        bytes memory callData,
        uint256 value
    )
    external
    override
    payable
    onlyOwner
    returns (bytes memory)
    {
        (bool result, bytes memory message) = target.call{value: value}(callData);
        require(result, "Failed to execute");
        return message;
    }
```

Due to its capacity, test coverage is expected to be 100%



### [L-05] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.

```solidity

contracts/smart-wallet/OwnableSmartWallet.sol:
   94:     function transferOwnership(address newOwner)
   95:         public
   96:         override(IOwnableSmartWallet, Ownable)
   97:     {
```

Recommended Mitigation Steps
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.


### [L-06] Avoid variable names that can shade

With global variable names in the form of  `call{value: value }` , argument name similarities can shade and negatively affect code readability.


```solidity

contracts/smart-wallet/OwnableSmartWallet.sol:
  77:     {
  78:         (bool result, bytes memory message) = target.call{value: value}(callData);
  79:         require(result, "Failed to execute");
  80:         return message;
  81:     }
```

### [L-07] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used `(0.8.13)`, newer version can be used `(0.8.17)`


### [L-08] Owner can renounce Ownership

**Context:**

[LiquidStakingManager.sol#L6](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L6)
[Syndicate.sol#L8](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L8)

**Description:**
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The StakeHouse Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.


`onlyOwner` functions;
```solidity
8 results - 2 files

contracts/smart-wallet/OwnableSmartWallet.sol:
   44          payable
   45:         onlyOwner // F: [OSW-6A]
   46          returns (bytes memory)

   59          payable
   60:         onlyOwner // F: [OSW-6A]
   61          returns (bytes memory)

   74      payable
   75:     onlyOwner
   76      returns (bytes memory)

  114:     function setApproval(address to, bool status) external onlyOwner override {

contracts/syndicate/Syndicate.sol:
  147:     ) external onlyOwner {

  154:     function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {

  161:     function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {

  168:     function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
```

**Recommendation:**
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.

### [L-09] _Lock pragmas_ to specific compiler version

**Description:**
Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally. 
https://swcregistry.io/docs/SWC-103

**Recommendation:**
Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
[solidity-specific/locking-pragmas](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/)

```solidity
24 files

pragma solidity ^0.8.13;
contracts/interfaces/IBrandNFT.sol:
contracts/interfaces/ILiquidStakingManagerChildContract.sol:
contracts/interfaces/ILPTokenInit.sol:
contracts/interfaces/ISyndicateFactory.sol:
contracts/interfaces/ISyndicateInit.sol:
contracts/interfaces/ITransferHookProcessor.sol:
contracts/liquid-staking/ETHPoolLPFactory.sol:
contracts/liquid-staking/GiantLP.sol:
contracts/liquid-staking/GiantMevAndFeesPool.sol:
contracts/liquid-staking/GiantPoolBase.sol:
contracts/liquid-staking/GiantSavETHVaultPool.sol:
contracts/liquid-staking/LiquidStakingManager.sol:
contracts/liquid-staking/LPToken.sol:
contracts/liquid-staking/LPTokenFactory.sol:
contracts/liquid-staking/LSDNFactory.sol:
contracts/liquid-staking/OptionalGatekeeperFactory.sol:
contracts/liquid-staking/OptionalHouseGatekeeper.sol:
contracts/liquid-staking/SavETHVault.sol:
contracts/liquid-staking/SavETHVaultDeployer.sol:
contracts/liquid-staking/StakingFundsVault.sol:
contracts/liquid-staking/StakingFundsVaultDeployer.sol:
contracts/liquid-staking/SyndicateRewardsProcessor.sol:
contracts/smart-wallet/OwnableSmartWallet.sol:
contracts/smart-wallet/OwnableSmartWalletFactory.sol:
```

### [L-10] Loss of precision due to rounding

Due to `/ PRECISION`, users can avoid paying fee if ` claimed [][]` result is below PRECISION

```solidity
contracts/liquid-staking/GiantMevAndFeesPool.sol:
  199  
  200:     /// @dev Internal re-usable method for setting claimed to max for msg.sender
  201:     function _setClaimedToMax(address _user) internal {
  202:         // New ETH stakers are not entitled to ETH earned by
  203:         claimed[_user][address(lpTokenETH)] = (accumulatedETHPerLPShare * lpTokenETH.balanceOf(_user)) / PRECISION;
  204:     }
```

**Recommendation:**
A lower limit can be added to the `claimed` values



### [L-11] Using vulnerable dependency of OpenZeppelin
The package.json configuration file says that the project is using 4.5.0 of OZ which has a not last update version

```js
1 result - 1 file

package.json:
  10:   "dependencies": {
  14:     "@openzeppelin/contracts": "^4.5.0",
  15:     "@openzeppelin/contracts-upgradeable": "4.5.0",
```
```js
VULNERABILITY	VULNERABLE VERSION
H    	Improper Verification of Cryptographic Signature	<4.7.3
M   	Denial of Service (DoS)	>=2.3.0 <4.7.2
L     	Incorrect Resource Transfer Between Spheres	 >=4.6.0 <4.7.2
H	Incorrect Calculation	>=4.3.0 <4.7.2
H	Information Exposure	>=4.1.0 <4.7.1
H	Information Exposure	>=4.0.0 <4.7.1
```

**Recommendation:**
Use patched versions
Latest non vulnerable version 4.8.0

### [L-12] Use ```safeTransferOwnership``` instead of ```transferOwnership``` function

**Context:**
[LiquidStakingManager.sol#L6](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L6)
[Syndicate.sol#L8](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L8)


```solidity
contracts/smart-wallet/OwnableSmartWallet.sol:
   93      /// @inheritdoc IOwnableSmartWallet
   94:     function transferOwnership(address newOwner)
   95:         public
   96:         override(IOwnableSmartWallet, Ownable)
   97:     {
   98:         // Only the owner themselves or an address that is approved for transfers
   99:         // is authorized to do this
  100:         require(
  101:             isTransferApproved(owner(), msg.sender),
  102:             "OwnableSmartWallet: Transfer is not allowed"
  103:         ); // F: [OSW-4]
  104: 
  105:         // Approval is revoked, in order to avoid unintended transfer allowance
  106:         // if this wallet ever returns to the previous owner
  107:         if (msg.sender != owner()) {
  108:             _setApproval(owner(), msg.sender, false); // F: [OSW-5]
  109:         }
  110:         _transferOwnership(newOwner); // F: [OSW-5]
  111:     }
```

**Description:**
```transferOwnership``` function is used to change Ownership

Use a 2 structure transferOwnership which is safer. 
```safeTransferOwnership```,  use it is more secure due to 2-stage ownership transfer.

**Recommendation:**
Use ``Ownable2Step.sol``
[Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)

```js
 /**
     * @dev The new owner accepts the ownership transfer.
     */
    function acceptOwnership() external {
        address sender = _msgSender();
        require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
        _transferOwnership(sender);
    }
}
```

### [NC-01] `0 address` check

0 address control should be done in these parts;

**Context:**
[GiantLP.sol#L20-L21](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L20-L21)
[LiquidStakingManager.sol#L170-L177](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L170-L177)
[LPToken.sol#L33-L34](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L33-L34)
[OptionalHouseGatekeeper.sol#L15](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L15)
[SavETHVault.sol#L45](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L45)
[Syndicate.sol#L130](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L130)
[SyndicateFactory.sol#L17](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L17)

**Recommendation:**
Add code like this;
`if (oracle == address(0)) revert ADDRESS_ZERO();`

### [NC-02] Add parameter to Event-Emit

Some event-emit description hasn’t parameter. Add to parameter  for front-end website or client app , they can has that something has happened on the blockchain.


```solidity
contracts/syndicate/Syndicate.sol:
  468      /// @dev Internal logic for initializing the syndicate contract
  469:     function _initialize(
  470:         address _contractOwner,
  471:         uint256 _priorityStakingEndBlock,
  472:         address[] memory _priorityStakers,
  473:         bytes[] memory _blsPubKeysForSyndicateKnots
  474:     ) internal {
  475:         // Transfer ownership from the deployer to the address specified as the owner
  476:         _transferOwnership(_contractOwner);
  477: 
  478:         // Add the initial set of knots to the syndicate
  479:         _registerKnotsToSyndicate(_blsPubKeysForSyndicateKnots);
  480: 
  481:         // Optionally process priority staking if the required params and array is configured
  482:         if (_priorityStakingEndBlock > block.number) {
  483:             priorityStakingEndBlock = _priorityStakingEndBlock;
  484:             _addPriorityStakers(_priorityStakers);
  485:         }
  486: 
  487:         emit ContractDeployed();
  488:     }
```

### [NC-03] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

Events with no old value;

```solidity

contracts/liquid-staking/LiquidStakingManager.sol:
  254      /// @notice Allow the DAO to rotate the network ticker before the network house is created
  255:     function updateTicker(string calldata _newTicker) external onlyDAO {
  256:         require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
  257:         require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
  258:         require(numberOfKnots == 0, "Cannot change ticker once house is created");
  259: 
  260:         stakehouseTicker = _newTicker;
  261: 
  262:         emit NetworkTickerUpdated(_newTicker);
  263      }
```
### [NC-04] Include ``return parameters`` in _NatSpec comments_

**Context:**
All Contracts

**Description:**

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

If Return parameters are declared, you must prefix them with "/// @return".

Some code analysis programs do analysis by reading NatSpec details, if they can't see the "@return" tag, they do incomplete analysis.

**Recommendation:**
Include return parameters in NatSpec comments

Recommendation Code Style:
 ```js
    /// @notice information about what a function does
    /// @param pageId The id of the page to get the URI for.
    /// @return Returns a page's URI if it has been minted 
    function tokenURI(uint256 pageId) public view virtual override returns (string memory) {
        if (pageId == 0 || pageId > currentId) revert("NOT_MINTED");

        return string.concat(BASE_URI, pageId.toString());
    }
```
### [NC-05] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used `(^0.8.13)`, newer version can be used `(0.8.17)`

### [NC-06] Solidity compiler optimizations can be problematic

```js
hardhat.config.js:
   3  
   4: module.exports = {
   5:   solidity: {
   6:     version: "0.8.13",
   7:     settings: {
   8:       optimizer: {
   9:         enabled: true,
  10:         runs: 200
  11:       }
```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.


**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.


### [NC-07] NatSpec is missing 

**Description:**
NatSpec is missing for the following functions , constructor and modifier:

```solidity

27 results

contracts/interfaces/IBrandNFT.sol:
  6:     function toLowerCase(string memory _base) external pure returns (string memory);
  7:     function lowercaseBrandTickerToTokenId(string memory _ticker) external returns (uint256);

contracts/interfaces/ILiquidStakingManager.sol:
  7:     function stakehouse() external view returns (address);

contracts/interfaces/ILiquidStakingManagerChildContract.sol:
  6:     function liquidStakingManager() external view returns (address);

contracts/interfaces/ILPTokenInit.sol:
  7:     function init(

contracts/interfaces/ISyndicateInit.sol:
  7:     function initialize(

contracts/interfaces/ITransferHookProcessor.sol:
  6:     function beforeTokenTransfer(address _from, address _to, uint256 _amount) external;
  7:     function afterTokenTransfer(address _from, address _to, uint256 _amount) external;

contracts/liquid-staking/GiantLP.sol:
  29:     function mint(address _recipient, uint256 _amount) external {
  34:     function burn(address _recipient, uint256 _amount) external {
  39:     function _beforeTokenTransfer(address _from, address _to, uint256 _amount) internal override {
  43:     function _afterTokenTransfer(address _from, address _to, uint256 _amount) internal override {

contracts/liquid-staking/OptionalGatekeeperFactory.sol:
  11:     function deploy(address _liquidStakingManager) external returns (OptionalHouseGatekeeper) {

contracts/liquid-staking/SavETHVault.sol:
   45:     function init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) external virtual initializer {

contracts/liquid-staking/SavETHVaultDeployer.sol:
  18:     function deploySavETHVault(address _liquidStakingManger, address _lpTokenFactory) external returns (address) {

contracts/smart-wallet/OwnableSmartWalletFactory.sol:
  28:     function createWallet() external returns (address wallet) {
  32:     function createWallet(address owner) external returns (address wallet) {
  36:     function _createWallet(address owner) internal returns (address wallet) {

contracts/smart-wallet/interfaces/IOwnableSmartWalletFactory.sol:
   9:     function createWallet() external returns (address wallet);
  11:     function createWallet(address owner) external returns (address wallet);
  13:     function walletExists(address wallet) external view returns (bool);

contracts/testing/interfaces/IFactoryDependencyInjector.sol:
   6:     function accountMan() external view returns (address);
   8:     function txRouter() external view returns (address);
  10:     function uni() external view returns (address);
  12:     function slot() external view returns (address);
  14:     function saveETHRegistry() external view returns (address);
  16:     function dETH() external view returns (address);
```
### [NC-08] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length


```solidity
9 results

contracts/syndicate/Syndicate.sol:
  216:             if (!isKnotRegistered[_blsPubKey] || isNoLongerPartOfSyndicate[_blsPubKey]) revert KnotIsNotRegisteredWithSyndicate();

  447:         return ((calculateETHForFreeFloatingOrCollateralizedHolders() - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);

 511:                     accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;

contracts/liquid-staking/ETHPoolLPFactory.sol:
  92:             getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfPreviousKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,

  97:             getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,

contracts/liquid-staking/GiantLP.sol:
  40:         if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).beforeTokenTransfer(_from, _to, _amount);

  46:         if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).afterTokenTransfer(_from, _to, _amount);

contracts/liquid-staking/GiantMevAndFeesPool.sol:
   97:         return _previewAccumulatedETH(_user, address(lpTokenETH), lpTokenETH.balanceOf(_user), lpTokenETH.totalSupply(), accumulated);

  118:             StakingFundsVault(payable(_stakingFundsVaults[i])).batchRotateLPTokens(_oldLPTokens[i], _newLPTokens[i], _amounts[i]);
```
### [NC-09] Missing Event for critical parameters change

```solidity
contracts/smart-wallet/OwnableSmartWallet.sol:
  66      /// @inheritdoc IOwnableSmartWallet
  67:     function rawExecute(
  68:         address target,
  69:         bytes memory callData,
  70:         uint256 value
  71:     )
  72:     external
  73:     override
  74:     payable
  75:     onlyOwner
  76:     returns (bytes memory)
  77:     {
  78:         (bool result, bytes memory message) = target.call{value: value}(callData);
  79:         require(result, "Failed to execute");
  80:         return message;
  81:     }
```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit


### [NC-10] Add to indexed parameter for countable Events

**Context:**
```solidity
contracts/liquid-staking/ETHPoolLPFactory.sol:
  15      /// @notice signalize withdrawing of ETH by depositor
  16:     event ETHWithdrawnByDepositor(address depositor, uint256 amount);
  17: 
  18:     /// @notice signalize burning of LP token
  19:     event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
  20: 
  21:     /// @notice signalize issuance of new LP token
  22:     event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
  23: 
  24:     /// @notice signalize issuance of existing LP token
  25:     event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```

**Description:**
Add to indexed parameter for countable Events

**Recommendation:**
Add Event-Emit


### [NC-11] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts

### [NC-12] Open TODOs

**Context:**
```solidity
1 result

contracts/syndicate/Syndicate.sol:
  194          } else {
  195:             // todo - check else case for any ETH lost
  196          }
```

**Recommendation:**
Use temporary TODOs as you work on a feature, but make sure to treat them before merging. Either add a link to a proper issue in your TODO, or remove it from the code.

### [NC-13] `Empty blocks` should be _removed_ or _Emit_ something

**Description:**
Code contains empty block

```js
10 results - 8 files

contracts/liquid-staking/GiantPoolBase.sol:
  101:     function _onDepositETH() internal virtual {}
  104:     function _onWithdraw(LPToken[] calldata _lpTokens) internal virtual {}
  
contracts/liquid-staking/LiquidStakingManager.sol:
  166:     constructor() initializer {}
  629:     receive() external payable {}

contracts/liquid-staking/LPToken.sol:
  28:     constructor() initializer {}
 
contracts/liquid-staking/SavETHVault.sol:
  43:     constructor() initializer {}
  
contracts/liquid-staking/StakingFundsVault.sol:
  43:     constructor() initializer {}
  
contracts/liquid-staking/SyndicateRewardsProcessor.sol:
  98:     receive() external payable {}

contracts/smart-wallet/OwnableSmartWallet.sol:
  25:     constructor() initializer {}
  
contracts/syndicate/Syndicate.sol:
  123:     constructor() initializer {}
```

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### [S-01] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```
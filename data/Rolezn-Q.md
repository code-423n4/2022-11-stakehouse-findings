## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Missing Checks for Address(0x0)  | 1 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Unused `receive()` Function Will Lock Ether In Contract  | 2 |
| [LOW&#x2011;3](#LOW&#x2011;3) | IERC20 `approve()` Is Deprecated | 1 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Use `_safeMint` instead of `_mint` | 2 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Missing Contract-existence Checks Before Low-level Calls | 12 |
| [LOW&#x2011;6](#LOW&#x2011;6) | Contracts are not using their OZ Upgradeable counterparts | 56 |
| [LOW&#x2011;7](#LOW&#x2011;7) | Missing parameter validation in `constructor` | 4 |
| [LOW&#x2011;8](#LOW&#x2011;8) | Upgrade OpenZeppelin Contract Dependency | 2 |
| [LOW&#x2011;9](#LOW&#x2011;9) | The `nonReentrant` modifier should occur before all other modifiers | 3 |

Total: 83 contexts over 9 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Redundant Cast | 1 |
| [NC&#x2011;2](#NC&#x2011;2) | Event Is Missing Indexed Fields | 14 |
| [NC&#x2011;3](#NC&#x2011;3) | Public Functions Not Called By The Contract Should Be Declared External Instead | 4 |
| [NC&#x2011;4](#NC&#x2011;4) | Implementation contract may not be initialized | 20 |
| [NC&#x2011;5](#NC&#x2011;5) | Avoid Floating Pragmas: The Version Should Be Locked | 18 |
| [NC&#x2011;6](#NC&#x2011;6) | Unused event may be unused code or indicative of missed emit/logic | 4 |
| [NC&#x2011;7](#NC&#x2011;7) | Lines are too long | 18 |
| [NC&#x2011;8](#NC&#x2011;8) | Use `bytes.concat()` | 2 |
| [NC&#x2011;9](#NC&#x2011;9) | No need for `== true` or `== false` checks | 16 |

Total: 97 contexts over 9 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Missing Checks for Address(0x0) 

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### <ins>Proof Of Concept</ins>


```solidity
38: deployer = _deployer;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol#L38


#### <ins>Recommended Mitigation Steps</ins>

Consider adding explicit zero-address validation on input parameters of address type.


### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Unused `receive()` Function Will Lock Ether In Contract 

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

#### <ins>Proof Of Concept</ins>


```solidity
629: receive() external payable {}
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L629

```solidity
98: receive() external payable {}
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98



#### <ins>Recommended Mitigation Steps</ins>

The function should call another function, otherwise it should revert



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> IERC20 `approve()` Is Deprecated

`approve()` is subject to a known front-running attack. It is deprecated in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. If only setting the initial allowance to the value that means infinite, `safeIncreaseAllowance()` can be used instead.

https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#IERC20-approve-address-uint256-

#### <ins>Proof Of Concept</ins>

```solidity
870: sETH.approve(syndicate, (2 ** 256) - 1);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L870



#### <ins>Recommended Mitigation Steps</ins>

Consider using `safeIncreaseAllowance()` / `safeDecreaseAllowance()` instead.



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Use `_safeMint` instead of `_mint`

According to openzepplin's ERC721, the use of `_mint` is discouraged, use _safeMint whenever possible.
https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-

#### <ins>Proof Of Concept</ins>


```solidity
31: _mint(_recipient, _amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L31

```solidity
47: _mint(_recipient, _amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol#L47



#### <ins>Recommended Mitigation Steps</ins>

Use `_safeMint` whenever possible instead of `_mint`



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```solidity
60: (bool success,) = msg.sender.call{value: _amount}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L60

```solidity
411: (bool transferResult, ) = _recipient.call{value: nodeRunnerAmount}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L411

```solidity
415: (transferResult, ) = dao.call{value: daoAmount}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L415

```solidity
460: (bool result,) = smartWallet.call{value: msg.value}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L460

```solidity
189: (bool result,) = msg.sender.call{value: _amount}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L189

```solidity
209: (bool result,) = _smartWallet.call{value: _amount}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L209

```solidity
190: (bool result,) = msg.sender.call{value: _amount}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L190

```solidity
244: (bool result,) = _wallet.call{value: _amount}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L244

```solidity
67: (bool success, ) = _recipient.call{value: due}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L67

```solidity
78: (bool result, bytes memory message) = target.call{value: value}(callData);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L78

```solidity
321: (bool success,) = _recipient.call{value: unclaimedUserShare}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L321

```solidity
668: (bool success,) = _recipient.call{value: unclaimedUserShare}("");
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L668




#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
5: import { Strings } from "@openzeppelin/contracts/utils/Strings.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L5

```solidity
5: import { ERC20 } from "@openzeppelin/contracts/token/ERC20/ERC20.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L5

```solidity
5: import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L5

```solidity
5: import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
6: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
7: import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
8: import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
9: import { Address } from "@openzeppelin/contracts/utils/Address.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L5-L9

```solidity
5: import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
6: import { ERC20PermitUpgradeable } from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-ERC20PermitUpgradeable.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol#L5-L6

```solidity
5: import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPTokenFactory.sol#L5

```solidity
5: import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L5

```solidity
5: import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
6: import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L5-L6

```solidity
5: import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L5

```solidity
5: import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
6: import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
7: import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L5-L7

```solidity
5: import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L5

```solidity
6: import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
7: import {Initializable} from "@openzeppelin/contracts/proxy/utils/Initializable.sol";
8: import {Address} from "@openzeppelin/contracts/utils/Address.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L6-L8

```solidity
5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L5

```solidity
5: import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
7: import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
8: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
9: import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L5-L9

```solidity
5: import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol#L5



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.



### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```solidity
25: pool = _pool;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L25

```solidity
26: transferHookProcessor = ITransferHookProcessor(_transferHookProcessor);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L26

```solidity
15: liquidStakingManager = ILiquidStakingManager(_manager);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L15

```solidity
17: syndicateImplementation = _syndicateImpl;
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol#L17



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.



### <a href="#Summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> Upgrade OpenZeppelin Contract Dependency

An outdated OZ version is used (which has known vulnerabilities, see: https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

#### <ins>Proof Of Concept</ins>


```solidity
    "@openzeppelin/contracts": "^4.5.0"
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/package.json#L15

```solidity
    "@openzeppelin/contracts-upgradeabale": "4.5.0"
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/package.json#L16



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json



### <a href="#Summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> The `nonReentrant` modifier should occur before all other modifiers

Currently the `nonReentrant` modifier is not the first to occur, it should occur before all other modifiers.

#### <ins>Proof Of Concept</ins>


```solidity
200: function withdrawETHForStaking(
        address _smartWallet,
        uint256 _amount
    ) public onlyManager nonReentrant returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L200

```solidity
239: function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L239

```solidity
255: function unstakeSyndicateSharesForRageQuit(
        address _sETHRecipient,
        bytes[] calldata _blsPublicKeys,
        uint256[] calldata _amounts
    ) external onlyManager nonReentrant {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L255



#### <ins>Recommended Mitigation Steps</ins>

Re-sort modifiers so that the `nonReentrant` modifier occurs first.



## Non Critical Issues



### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Redundant Cast

The type of the variable is the same as the type to which the variable is being cast

#### <ins>Proof Of Concept</ins>


```solidity
33: address(_deployer)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPTokenFactory.sol#L33






### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Event Is Missing Indexed Fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 

Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

#### <ins>Proof Of Concept</ins>


```solidity
event ETHWithdrawnByDepositor(address depositor, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L16

```solidity
event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L19

```solidity
event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L22

```solidity
event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L25

```solidity
event DAOCommissionUpdated(uint256 old, uint256 newCommission);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L84

```solidity
event DETHRedeemed(address depositor, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L19

```solidity
event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L22

```solidity
event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L121

```solidity
event ETHDeposited(address sender, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L25

```solidity
event ETHWithdrawn(address receiver, address admin, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L28

```solidity
event ERC20Recovered(address admin, address recipient, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L31

```solidity
event WETHUnwrapped(address admin, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L34

```solidity
event Staked(bytes BLSPubKey, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L57

```solidity
event UnStaked(bytes BLSPubKey, uint256 amount);
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L60






### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


```solidity
function depositETH(uint256 _amount) public payable {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L34

```solidity
function deployNewLiquidStakingDerivativeNetwork(
        address _dao,
        uint256 _optionalCommission,
        bool _deployOptionalHouseGatekeeper,
        string calldata _stakehouseTicker
    ) public returns (address) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L73

```solidity
function withdrawETHForStaking(
        address _smartWallet,
        uint256 _amount
    ) public onlyManager nonReentrant returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L200

```solidity
function deploySyndicate(
        address _contractOwner,
        uint256 _priorityStakingEndBlock,
        address[] calldata _priorityStakers,
        bytes[] calldata _blsPubKeysForSyndicateKnots
    ) public override returns (address) {
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol#L21






### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
19: constructor(
        address _pool,
        address _transferHookProcessor,
        string memory _name,
        string memory _symbol
    ) ERC20(_name, _symbol)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L19

```solidity
17: constructor(LSDNFactory _factory)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L17

```solidity
18: constructor(LSDNFactory _factory)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L18

```solidity
18: constructor(address _lpTokenImplementation)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPTokenFactory.sol#L18

```solidity
41: constructor(
        address _liquidStakingManagerImplementation,
        address _syndicateFactory,
        address _lpTokenFactory,
        address _smartWalletFactory,
        address _brand,
        address _savETHVaultDeployer,
        address _stakingFundsVaultDeployer,
        address _optionalGatekeeperDeployer
    )
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L41

```solidity
14: constructor(address _manager)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L14

```solidity
14: constructor()
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L14

```solidity
14: constructor()
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L14

```solidity
22: constructor()
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L22

```solidity
16: constructor(address _syndicateImpl)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/SyndicateFactory.sol#L16





### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Avoid Floating Pragmas: The Version Should Be Locked

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### <ins>Proof Of Concept</ins>

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L3

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantLP.sol#L1

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L1

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantPoolBase.sol#L1

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L1

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L3

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPToken.sol#L1

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LPTokenFactory.sol#L1

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LSDNFactory.sol#L1

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L3

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L1

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L3

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L1

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L3

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L3

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L3

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWallet.sol#L3

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L3






### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Unused event may be unused code or indicative of missed emit/logic

Events that are declared but not used may be indicative of unused declarations where it makes sense to remove them for better readability/maintainability/auditability, or worse indicative of a missing emit which is bad for monitoring or missing logic that would have emitted that event.

#### <ins>Proof Of Concept</ins>


```solidity
121: event CurrentStamp
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L121

```solidity
31: event ERC20Recovered
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L31

```solidity
34: event WETHUnwrapped
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L34

```solidity
45: event CollateralizedSLOTReCalibrated
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L45



#### <ins>Recommended Mitigation Steps</ins>
Add emit or remove event declaration.



### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```solidity
222: /// @notice In preparation of a rage quit, restore sETH to a smart wallet which are recoverable with the execution methods in the event this step does not go to plan
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L222

```solidity
98: /// @param _amounts Amount of each LP token that the user wants to burn in exchange for either ETH (if not staked) or dETH (if derivatives minted)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L98

```solidity
111: /// @param _amounts Amount of each LP token that the user wants to burn in exchange for either ETH (if not staked) or dETH (if derivatives minted)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L111

```solidity
222: /// @notice Utility function that proxies through to the liquid staking manager to check whether the BLS key ever registered with the network but is now banned
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L222

```solidity
300: // Looking at this contract balance and the ETH that is yet to be transferred from the syndicate, then tell the user how much ETH they have earned
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L300

```solidity
35: /// @dev This contract can be extended to allow lending and borrowing of time slots for borrower to redeem any revenue generated within the specified window
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L35

```solidity
95: /// @notice Syndicate deployer can highlight addresses that get priority for staking free floating house sETH up to a certain block before anyone can do it
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L95

```solidity
116: /// @notice Whether a BLS public key, that has been previously registered, is no longer part of the syndicate and its shares (free floating or SLOT) cannot earn any more rewards
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L116

```solidity
119: /// @notice Once a BLS public key is no longer part of the syndicate, the accumulated ETH per free floating SLOT share is snapshotted so historical earnings can be drawn down correctly
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L119

```solidity
341: /// @notice For any new ETH received by the syndicate, at the knot level allocate ETH owed to each collateralized owner and do it for a batch of knots
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L341

```solidity
356: /// @notice Calculate the amount of unclaimed ETH for a given BLS publice key + free floating SLOT staker without factoring in unprocessed rewards
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L356

```solidity
372: /// @notice Using `highestSeenBalance`, this is the amount that is separately allocated to either free floating or collateralized SLOT holders
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L372

```solidity
381: /// @notice Preview the amount of unclaimed ETH available for an sETH staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L381

```solidity
398: /// @notice Preview the amount of unclaimed ETH available for a collatearlized SLOT staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L398

```solidity
490: /// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this amongs the current set of collateralized owners (a dynamic set of addresses and balances)
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L490

```solidity
596: /// @dev Business logic for de-registering a set of knots from the syndicate and doing the required snapshots to ensure historical earnings are preserved
```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L596





### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
135: string memory tokenName = string(abi.encodePacked(baseLPTokenName, tokenNumber)
136: string memory tokenSymbol = string(abi.encodePacked(baseLPTokenSymbol, tokenNumber)

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L135-L136




#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> No need for `== true` or `== false` checks

There is no need to verify that `== true` or `== false` when the variable checked upon is a boolean as well.

#### <ins>Proof Of Concept</ins>


```solidity
291: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L291

```solidity
328: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
332: require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L328

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L332


```solidity
393: require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L393

```solidity
436: require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
437: require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
469: require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L436

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L437

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L469


```solidity
541: require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L541

```solidity
589: require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L589

```solidity
688: require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/LiquidStakingManager.sol#L688

```solidity
64: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L64

```solidity
84: require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/SavETHVault.sol#L84

```solidity
79: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L79

```solidity
114: require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/liquid-staking/StakingFundsVault.sol#L114

```solidity
611: if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();
612: if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();

```

https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts/syndicate/Syndicate.sol#L611-L612






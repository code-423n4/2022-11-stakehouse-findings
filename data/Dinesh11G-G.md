### Don't Initialize Variables with Default Value

#### Impact
Issue Information: Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
812 => for (uint i=0; i < b.length; i++) {
914 => for (uint256 i = 0; i < reads.length; i++) {
1069 => for (uint i = 0; i < max; i++) {
1078 => for (uint256 i = 0; i < b.length; i++) {
```
#### Tools used
Manual



========================================================================================



### Cache Array Length Outside of Loop

#### Impact
Issue Information: Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::58 => uint256 numOfRotations = _oldLPTokens.length;
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::60 => require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::61 => require(numOfRotations == _amounts.length, "Inconsistent arrays");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::88 => require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::89 => require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::112 => require(_blsPublicKeyOfKnot.length == 48, "Invalid BLS public key");
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::30 => calculateDeploymentSalt(msg.sender, _contractOwner, _blsPubKeysForSyndicateKnots.length)
stakehouse/MockBrandNFT.sol::22 => bytes(_ticker).length >= 3 && bytes(_ticker).length <= 5,
stakehouse/MockBrandNFT.sol::46 => bytes memory bLower = new bytes(bStr.length);
stakehouse/MockBrandNFT.sol::47 => for (uint256 i; i < bStr.length; ++i) {
console.sol::8 => uint256 payloadLength = payload.length;
console2.sol::14 => uint256 payloadLength = payload.length;
```
#### Tools used
Manual



=======================================================================================



### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");

#### Findings:
```
stakehouse/MockSavETHRegistry.sol::30 => return balInIndex[_indexId][_blsKey] > 0 ? balInIndex[_indexId][_blsKey] : 24 ether ;
src/test.sol::83 => return hevmCodeSize > 0;
1106 => return uint256(a > 0 ? a : -a);
foundry/StakingFundsVault.t.sol::35 => require(_blsPubKeys.length > 0, "Empty array");
```
#### Tools used
Manual


=====================================================================================



### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::63 => return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));

```
#### Tools used
Manual



=====================================================================================




### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::5 => import { Strings } from "@openzeppelin/contracts/utils/Strings.sol";
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::6 => import { StakehouseAPI } from "@blockswaplab/stakehouse-solidity-api/contracts/StakehouseAPI.sol";
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::7 => import { IDataStructures } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/IDataStructures.sol";
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::122 => require(lpToken.totalSupply() + _amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol::130 => require(_amount <= maxStakingAmountPerValidator, "Amount exceeds the staking limit for the validator");
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::5 => import { ERC20 } from "@openzeppelin/contracts/token/ERC20/ERC20.sol";
2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol::6 => import { ITransferHookProcessor } from "../interfaces/ITransferHookProcessor.sol";
2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol::12 => import { ITransferHookProcessor } from "../interfaces/ITransferHookProcessor.sol";
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::5 => import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol::55 => require(idleETH >= _amount, "Come back later or withdraw less ETH");
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::5 => import { StakehouseAPI } from "@blockswaplab/stakehouse-solidity-api/contracts/StakehouseAPI.sol";
2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol::151 => "ETH is either staked or derivatives minted"
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::6 => import { ERC20PermitUpgradeable } from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-ERC20PermitUpgradeable.sol";
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::8 => import { ILiquidStakingManagerChildContract } from "../interfaces/ILiquidStakingManagerChildContract.sol";
2022-11-stakehouse/contracts/liquid-staking/LPToken.sol::9 => import { ITransferHookProcessor } from "../interfaces/ITransferHookProcessor.sol";
2022-11-stakehouse/contracts/liquid-staking/LPTokenFactory.sol::5 => import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::5 => import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";
2022-11-stakehouse/contracts/liquid-staking/LSDNFactory.sol::6 => import { ILiquidStakingManager } from "../interfaces/ILiquidStakingManager.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::6 => import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::7 => import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::8 => import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::9 => import { Address } from "@openzeppelin/contracts/utils/Address.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::11 => import { StakehouseAPI } from "@blockswaplab/stakehouse-solidity-api/contracts/StakehouseAPI.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::12 => import { ITransactionRouter } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/ITransactionRouter.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::13 => import { IDataStructures } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/IDataStructures.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::14 => import { IStakeHouseRegistry } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/IStakeHouseRegistry.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::22 => import { SyndicateFactory } from "../syndicate/SyndicateFactory.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::26 => import { OwnableSmartWalletFactory } from "../smart-wallet/OwnableSmartWalletFactory.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::27 => import { IOwnableSmartWalletFactory } from "../smart-wallet/interfaces/IOwnableSmartWalletFactory.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::28 => import { IOwnableSmartWallet } from "../smart-wallet/interfaces/IOwnableSmartWallet.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::29 => import { ISyndicateFactory } from "../interfaces/ISyndicateFactory.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::30 => import { ILiquidStakingManager } from "../interfaces/ILiquidStakingManager.sol";
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::256 => require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::257 => require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::258 => require(numberOfKnots == 0, "Cannot change ticker once house is created");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::268 => require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::280 => require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::291 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::328 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::331 => require(smartWalletOfNodeRunner[msg.sender] == associatedSmartWallet, "Not the node runner for the smart wallet ");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::332 => require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::393 => require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::396 => require(smartWalletOfKnot[_blsPubKeys[i]] == smartWallet, "BLS public key doesn't belong to the node runner");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::435 => require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::437 => require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::455 => require(smartWalletRepresentative[smartWallet] == _eoaRepresentative, "Different EOA specified - rotate outside");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::469 => require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::541 => require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::589 => require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::662 => require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::663 => require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::936 => require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::939 => require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::940 => require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::944 => require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");
2022-11-stakehouse/contracts/liquid-staking/OptionalHouseGatekeeper.sol::6 => import { ILiquidStakingManager } from "../interfaces/ILiquidStakingManager.sol";
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::6 => import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::7 => import { IDataStructures } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/IDataStructures.sol";
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::9 => import { ILiquidStakingManager } from "../interfaces/ILiquidStakingManager.sol";
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::64 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::84 => require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::90 => require(msg.value == _amount, "Must provide correct amount of ETH");
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::204 => require(_amount >= 24 ether, "Amount cannot be less than 24 ether");
2022-11-stakehouse/contracts/liquid-staking/SavETHVaultDeployer.sol::5 => import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::6 => import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::7 => import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::8 => import { StakehouseAPI } from "@blockswaplab/stakehouse-solidity-api/contracts/StakehouseAPI.sol";
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::9 => import { IDataStructures } from "@blockswaplab/stakehouse-contract-interfaces/contracts/interfaces/IDataStructures.sol";
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::11 => import { ITransferHookProcessor } from "../interfaces/ITransferHookProcessor.sol";
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::79 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::114 => require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::120 => require(msg.value == _amount, "Must provide correct amount of ETH");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::154 => require(address(token) != address(0), "No ETH staked for specified BLS key");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::240 => require(_amount >= 4 ether, "Amount cannot be less than 4 ether");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol::267 => require(syndicate.isNoLongerPartOfSyndicate(_blsPublicKeys[i]), "Knot is still active in syndicate");
2022-11-stakehouse/contracts/liquid-staking/StakingFundsVaultDeployer.sol::5 => import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::5 => import {IOwnableSmartWallet} from "./interfaces/IOwnableSmartWallet.sol";
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::6 => import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::7 => import {Initializable} from "@openzeppelin/contracts/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::8 => import {Address} from "@openzeppelin/contracts/utils/Address.sol";
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::35 => "OwnableSmartWallet: Attempting to initialize with zero address owner"
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::102 => "OwnableSmartWallet: Transfer is not allowed"
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWallet.sol::117 => "OwnableSmartWallet: Approval cannot be set for zero address"
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::5 => import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::7 => import {IOwnableSmartWallet} from "./interfaces/IOwnableSmartWallet.sol";
2022-11-stakehouse/contracts/smart-wallet/OwnableSmartWalletFactory.sol::8 => import {IOwnableSmartWalletFactory} from "./interfaces/IOwnableSmartWalletFactory.sol";
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::5 => import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::6 => import { StakehouseAPI } from "@blockswaplab/stakehouse-solidity-api/contracts/StakehouseAPI.sol";
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::7 => import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::8 => import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::9 => import { ReentrancyGuard } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::5 => import { Clones } from "@openzeppelin/contracts/proxy/Clones.sol";
2022-11-stakehouse/contracts/syndicate/SyndicateFactory.sol::6 => import { ISyndicateFactory } from "../interfaces/ISyndicateFactory.sol";
```
#### Tools used
Manual




=======================================================================================



### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;
#### Findings:
```
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::434 => require(msg.value == len * 4 ether, "Insufficient ether provided");
2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol::870 => sETH.approve(syndicate, (2 ** 256) - 1);
2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol::174 => redemptionValue = (dETHDetails.savETHBalance * _amount) / 24 ether;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::378 => return ethPerKnot / 2;
2022-11-stakehouse/contracts/syndicate/Syndicate.sol::546 => return (_ethSinceLastUpdate * PRECISION) / (numberOfRegisteredKnots * 4 ether);
```
#### Tools used
Manual
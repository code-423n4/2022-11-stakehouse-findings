## QA Report: low risk

### Unused/empty `receive()/fallback()` function
Leaving the `receive()` below without a `revert` could result in a loss of funds for a user who sends Ether to the contract (having no way to get anything back)
 
[LiquidStakingManager.sol: L629](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L629)
```solidity
    receive() external payable {}
```
Similarly for the following:

[SyndicateRewardsProcessor.sol: L98](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98)

[OwnableSmartWallet.sol: L148-150](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWallet.sol#L148-L150)
___
___


### Missing checks for address(0x0) when assigning values to address state variables
___
Check for address(0x0) is missing for `_pool`:

[GiantLP.sol: L25](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L25)
```solidity
        pool = _pool;
```
___
Check for address(0x0) is missing for `_deployer`:

[LPToken.sol: L38](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPToken.sol#L38)
```solidity
        deployer = _deployer;
```
___
___


###  Upgrade Open Zeppelin
An outdated Open Zeppelin version (`4.5.0`) is used.

The version used has known vulnerabilties, all of which have been patched by version `4.7.3`. See [Security Advisories](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories). 
___
___


## QA Report: non-critical

### Typos
___
[SavETHVault.sol: L115](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L115)
```solidity
        require(numOfTokens == _amounts.length, "Inconsisent array length");
```
Change `Inconsisent` to `Inconsistent`
___
[SavETHVault.sol: L227](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L227)
```solidity
    /// @notice Utility function that determins whether an LP can be burned for dETH if the associated derivatives have been minted
```
Change `determins` to `determines`
___
[GiantMevAndFeesPool.sol: L103](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L103)
```solidity
    /// @param _oldLPTokens List of new savETH vault LP tokens that the vault wants to receive in exchange for moving ETH to a new KNOT
```
Change ` _oldLPTokens` to ` _newLPTokens`
___
[LiquidStakingManager.sol: L101](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L101)
```solidity
    /// @notice address of optional gatekeeper for admiting new knots to the house created by the network
```
Change `admiting` to `admitting`
___
[LiquidStakingManager.sol: L436](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L436)
```solidity
        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
```
Change `Unrecognised` to `Unrecognized`, assuming American English. The in-scope contracts use mostly U.S. rather than British spellings. In any case, a choice of spelling version should be made for consistency.
___
[LiquidStakingManager.sol: L476](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L476)
```solidity
            // register validtor initals for each of the KNOTs
```
Change `validtor` to `validator` and `initals` to `initials`
___
The same typo (`overriden`) occurs in both lines referenced below:

[LiquidStakingManager.sol: L903](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L903)

[LiquidStakingManager.sol: L920](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L920)
```solidity
    /// @dev Something that can be overriden during testing
```
Change `overriden` to `overridden` in both cases
___
[LiquidStakingManager.sol: L920](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L920)
```solidity
    /// @dev This can be overriden to customise fee percentages
```
Change `customise` to `customize`, again assuming American English
___
[ETHPoolLPFactory.sol: L74](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L74)
```solidity
    /// @param _newLPToken Instane of the new LP token (to be minted)
```
Change `Instane` to `Instance`
___
The same typo (`depoistor`) occurs in both lines referenced below:

[ETHPoolLPFactory.sol: L124](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L124)

[ETHPoolLPFactory.sol: L150](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L150)
```solidity
            // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied
```
Change `depoistor` to `depositor` in both cases
___
___

### Duplicate import
The import of `LPToken` occurs twice in `GiantMevAndFeesPool.sol`

[GiantMevAndFeesPool.sol: L5-12](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L5-L12)
```solidity
import { GiantLP } from "./GiantLP.sol";
import { StakingFundsVault } from "./StakingFundsVault.sol";
import { LPToken } from "./LPToken.sol";
import { GiantPoolBase } from "./GiantPoolBase.sol";
import { SyndicateRewardsProcessor } from "./SyndicateRewardsProcessor.sol";
import { LSDNFactory } from "./LSDNFactory.sol";
import { LPToken } from "./LPToken.sol";
import { ITransferHookProcessor } from "../interfaces/ITransferHookProcessor.sol";
```
Recommendation: Remove duplicate import
___
___

### Event is missing `indexed` fields
Each `event` should use three `indexed` fields if there are three or more fields
___
[GiantPoolBase.sol: L19](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L19)
```solidity
    event LPSwappedForVaultLP(address indexed vaultLPToken, address indexed sender, uint256 amount);
```
___
[GiantSavETHVaultPool.sol: L16](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L16)
```solidity
    event LPBurnedForDETH(address indexed savETHVaultLPToken, address indexed sender, uint256 amount);
```
___
[SavETHVault.sol: L22](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L22)
```solidity
    event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
```
___
[SavETHVault.sol: L121](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L121)
```solidity
    event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```
___
[StakingFundsVault.sol: L28](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L28)
```solidity
    event ETHWithdrawn(address receiver, address admin, uint256 amount);
```
___
[StakingFundsVault.sol: L31](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L31)
```solidity
    event ERC20Recovered(address admin, address recipient, uint256 amount);
```
___
[Syndicate.sol: L63](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L63)
```solidity
    event ETHClaimed(bytes BLSPubKey, address indexed user, address recipient, uint256 claim, bool indexed isCollateralizedClaim);
```
___
[LiquidStakingManager.sol: L66](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L66)
```solidity
    event ETHWithdrawnFromSmartWallet(address indexed associatedSmartWallet, bytes blsPublicKeyOfKnot, address nodeRunner);
```
___
[SyndicateRewardsProcessor.sol: L12](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L12)
```solidity
    event ETHDistributed(address indexed user, address indexed recipient, uint256 amount);
```
___
[ETHPoolLPFactory.sol: L19](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L19)
```solidity
    event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```
___
[ETHPoolLPFactory.sol: L22](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L22)
```solidity
    event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
```
___
[ETHPoolLPFactory.sol: L25](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L25)
```solidity
    event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```
___
___


### Inconsistent long comment wrap syntax reduces readability
Long comments are wrapped using inconsistent spacing after the `///` or `//`. While the choice of spacing is up to the developers, it should be made constant throughout, as the suggestions below (which use 2 additional spaces) show: 

___
[OwnableSmartWalletFactory.sol: L17-19](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L17-L19)
```solidity
    /// @notice Can be used to verify that the address is actually
    ///         OwnableSmartWallet and not an impersonating malicious
    ///         account
```
Suggestion:
```solidity
    /// @notice Can be used to verify that the address is actually 
    ///   OwnableSmartWallet and not an impersonating malicious account
```
___
[OwnableSmartWallet.sol: L19-21](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWallet.sol#L19-L21)
```solidity
    /// @dev A map from owner and spender to transfer approval. Determines whether
    ///      the spender can transfer this wallet from the owner. Can be used
    ///      to put this wallet in possession of a strategy (e.g., as collateral).
```
Suggestion:
```solidity
    /// @dev A map from owner and spender to transfer approval. Determines whether 
    ///   the spender can transfer this wallet from the owner. Can be used
    ///   to put this wallet in possession of a strategy (e.g., as collateral).
```
___
[SavETHVault.sol: L31-33](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L31-L33)
```solidity
    /// @dev If dETH is not withdrawn, then for a non-existing dETH balance
    /// the structure would result in zero balance even though dETH isn't withdrawn for KNOT
    /// withdrawn parameter tracks the status of dETH for a KNOT
```
Suggestion:
```solidity
    /// @dev If dETH is not withdrawn, then for a non-existing dETH balance the  
    ///   structure would result in zero balance even though dETH isn't withdrawn
    ///   since KNOT withdrawn parameter tracks the status of dETH for a KNOT
```
___
___


### Long single-line comments 
In theory, comments over 80 characters should wrap using multi-line comment syntax. Even if longer comments (up to 120 characters) are acceptable and a scroll bar is provided, very long comments can interfere with readability. As noted in the previous finding, many of the long comments in `Stakehouse` do wrap. Below are five of the remaining extra-long lines:
___
[Syndicate.sol: L119](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L119)
```solidity
    /// @notice Once a BLS public key is no longer part of the syndicate, the accumulated ETH per free floating SLOT share is snapshotted so historical earnings can be drawn down correctly
```
Suggestion:
```solidity
    /// @notice Once a BLS public key is no longer part of the syndicate,
    ///   the accumulated ETH per free floating SLOT share is snapshotted 
    ///   so historical earnings can be drawn down correctly.
```
___
[LiquidStakingManager.sol: L222](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L222)
```solidity
    /// @notice In preparation of a rage quit, restore sETH to a smart wallet which are recoverable with the execution methods in the event this step does not go to plan
```
Suggestion:
```solidity
    /// @notice In preparation of a rage quit, restore sETH to a smart wallet which are 
    ///   recoverable with the execution methods in the event this step does not go to plan.
```
___
[SavETHVault.sol: L222](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L222)
```solidity
    /// @notice Utility function that proxies through to the liquid staking manager to check whether the BLS key ever registered with the network but is now banned
```
Suggestion:
```solidity
    /// @notice Utility function that proxies through to the liquid staking manager to check 
    ///   whether the BLS key ever registered with the network but is now banned.
```
___
[LSDNFactory.sol: L35](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LSDNFactory.sol#L35)
```solidity
    /// @notice Address of the contract that can deploy new instances of optional gatekeepers for controlling which knots can join the LSDN house
```
Suggestion:
```solidity
    /// @notice Address of the contract that can deploy new instances of optional  
    ///   gatekeepers for controlling which knots can join the LSDN house.
```
___
[GiantSavETHVaultPool.sol: L65](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L65)
```solidity
    /// @param _amounts Amounts of giant LP the user owns which is burnt 1:1 with savETH vault LP and in turn that will give a share of dETH
```
Suggestion:
```solidity
    /// @param _amounts Amounts of giant LP the user owns which is burnt 1:1  
    ///   with savETH vault LP and in turn that will give a share of dETH.
```
___

Similarly for the following extra-long (at least 140 characters) comment lines:

[GiantSavETHVaultPool.sol: L111](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L111)

[SavETHVault.sol: L111](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L111)

[SavETHVault.sol: L217](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L217)

[Syndicate.sol: L35](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L35)

[Syndicate.sol: L95](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L95)

[Syndicate.sol: L116](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L116)

[LiquidStakingManager.sol: L933](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L933)
___
___

### Update sensitive terms in both comments and code
Terms incorporating "white," "black," "master" or "slave" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/)
___
[LiquidStakingManager.sol: L38-39](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L38-L39)
```solidity
    /// @notice signalize updated whitelist status of node runner
    event NodeRunnerWhitelistingStatusChanged(address indexed nodeRunner, bool updatedStatus);
```
Suggestion: Change `whitelist` to `allowlist` and `NodeRunnerWhitelistingStatusChanged` to `NodeRunnerAllowlistingStatusChanged`

Similarly for the following instances of `whitelist` and its variants:

[LiquidStakingManager.sol: L35-36](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L35-L36)

[LiquidStakingManager.sol: L119-123](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L119-L123)

[LiquidStakingManager.sol: L265-283](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L265-L283)

[LiquidStakingManager.sol: L453](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L453)

[LiquidStakingManager.sol: L681](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L681)

[LiquidStakingManager.sol: L687](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L687)
___
[OwnableSmartWalletFactory.sol: L14](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L14)
```solidity
    address public immutable masterWallet;
```
Suggestion: Change `masterWallet` to `primaryWallet`

Similarly for the following instances of `masterWallet`:

[OwnableSmartWalletFactory.sol: L23-25](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L23-L25)

[OwnableSmartWalletFactory.sol: L39](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L39)
___
___

### Missing NatSpec

NatSpec is completely missing for the following `constructor`:

[SavETHVaultDeployer.sol: L14-16](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVaultDeployer.sol#L14-L16)
```solidity
    constructor() {
        implementation = address(new SavETHVault());
    }
```

Natspec is also absent or mostly absent for the following `constructors`:

[StakingFundsVaultDeployer.sol: L14-16](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L14-L16)

[LSDNFactory.sol: L41-50](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LSDNFactory.sol#L41-L50)
___
___

NatSpec is completely missing for the following `event`:

[OptionalGatekeeperFactory.sol: L9](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L9)
```solidity
    event NewOptionalGatekeeperDeployed(address indexed keeper, address indexed manager);
```
Note that NatSpec for this `event` would be expected to include an `@notice` statement (or its equivalent) and `@param` statements for `keeper` and `manager`

NatSpec is also absent for the following `events`:

[SavETHVaultDeployer.sol: L11](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVaultDeployer.sol#L11)

[StakingFundsVaultDeployer.sol: L11](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L11)

[SavETHVault.sol: L121](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L121)
___
___

`@param` statement is missing for the following `event`:

[LPTokenFactory.sol: L11-12](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPTokenFactory.sol#L11-L12)
```solidity
    /// @notice Emitted when a new LP token instance is deployed
    event LPTokenDeployed(address indexed factoryCloneToken);
```
One or more `@param` statements are also missing for the following `events`:

[GiantPoolBase.sol: L12-13](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L12-L13)

[GiantPoolBase.sol: L15-16](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L15-L16)

[GiantPoolBase.sol: L18-19](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L18-L19)

[LSDNFactory.sol: L11-12](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LSDNFactory.sol#L11-L12)

[GiantSavETHVaultPool.sol: L15-16](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L15-L16)

[SavETHVault.sol: L18-19](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L18-L19)

[SavETHVault.sol: L21-22](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L21-L22)

[StakingFundsVault.sol: L24-25](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L24-L25)

[StakingFundsVault.sol: L27-28](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L27-L28)

[StakingFundsVault.sol: L30-31](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L30-L31)

[StakingFundsVault.sol: L33-34](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L33-L34)

[Syndicate.sol: L41-42](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L41-L42)

[Syndicate.sol: L44-45](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L44-L45)

[Syndicate.sol: L47-48](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L47-L48)

[Syndicate.sol: L50-51](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L50-L51)

[Syndicate.sol: L53-54](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L53-L54)

[Syndicate.sol: L56-57](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L56-L57)

[Syndicate.sol: L59-60](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L59-L60)

[Syndicate.sol: L62-63](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L62-L63)

[LiquidStakingManager.sol: L35-36](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L35-L36)

[LiquidStakingManager.sol: L38-39](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L38-L39)

[LiquidStakingManager.sol: L41-42](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L41-L42)

[LiquidStakingManager.sol: L44-45](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L44-L45)

[LiquidStakingManager.sol: L47-48](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L47-L48)

[LiquidStakingManager.sol: L50-51](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L50-L51)

[LiquidStakingManager.sol: L53-54](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L53-L54)

[LiquidStakingManager.sol: L56-57](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L56-L57)

[LiquidStakingManager.sol: L59-60](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L59-L60)

[LiquidStakingManager.sol: L62-63](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L62-L63)

[LiquidStakingManager.sol: L65-66](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L65-L66)

[LiquidStakingManager.sol: L68-69](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L68-L69)

[LiquidStakingManager.sol: L71-72](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L71-L72)

[LiquidStakingManager.sol: L74-75](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L74-L75)

[LiquidStakingManager.sol: L77-78](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L77-L78)

[LiquidStakingManager.sol: L80-81](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L80-L81)

[LiquidStakingManager.sol: L83-84](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L83-L84)

[LiquidStakingManager.sol: L86-87](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L86-L87)

[SyndicateRewardsProcessor.sol: L8-9](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L8-L9)

[SyndicateRewardsProcessor.sol: L11-12](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L11-L12)

[ETHPoolLPFactory.sol: L15-16](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L15-L16)

[ETHPoolLPFactory.sol: L18-19](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L18-L19)

[ETHPoolLPFactory.sol: L21-22](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L21-L22)

[ETHPoolLPFactory.sol: L24-25](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L24-L25)
___
___

NatSpec is completely missing for the following `function`:

[OptionalGatekeeperFactory.sol: L11-16](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L11-L16)
```solidity
    function deploy(address _liquidStakingManager) external returns (OptionalHouseGatekeeper) {
        OptionalHouseGatekeeper newKeeper = new OptionalHouseGatekeeper(_liquidStakingManager);

        emit NewOptionalGatekeeperDeployed(address(newKeeper), _liquidStakingManager);

        return newKeeper;
    }
```
Natspec is also absent for the `public` or `external` `functions` below (NatSpec is not required for `functions` marked `private` or `internal`):

[SavETHVaultDeployer.sol: L18](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVaultDeployer.sol#L18)

[StakingFundsVaultDeployer.sol: L18](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L18)

[OwnableSmartWalletFactory.sol: L28](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L28)
 
[OwnableSmartWalletFactory.sol: L32](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L32)

[OptionalGatekeeperFactory.sol: L11](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L11)

[GiantLP.sol: L29](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L29)

[GiantLP.sol: L34](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L34)

[SavETHVault.sol: L45](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L45)
___
___

`@param` and `@return` statements are missing for the `function` below:

[OptionalHouseGatekeeper.sol: L18-21](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L18-L21)
```solidity
    /// @notice Method called by the house before admitting a new KNOT member and giving house sETH
    function isMemberPermitted(bytes calldata _blsPublicKeyOfKnot) external override view returns (bool) {
        return liquidStakingManager.isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot);
    }
```
Similarly, one or more `@param` and/or `@return` statements are missing for the following `functions`:

[LPTokenFactory.sol: L24-32](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPTokenFactory.sol#L24-L32)

[LPToken.sol: L30-37](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPToken.sol#L30-L37)

[LPToken.sol: L44-46](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPToken.sol#L44-L46)

[LPToken.sol: L50-51](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPToken.sol#L50-L51)

[LPToken.sol: L55-56](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPToken.sol#L55-L56)

[GiantPoolBase.sol: L33-34](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L33-L34)

[LSDNFactory.sol: L70-78](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LSDNFactory.sol#L70-L78)

[SavETHVault.sol: L79-83](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L79-L83)

[SavETHVault.sol: L217-218](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L217-L218)

[SavETHVault.sol: L222-223](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L222-L223)

[SavETHVault.sol: L227-228](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L227-L228)

[GiantMevAndFeesPool.sol: L55-60](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L55-L60)

[GiantMevAndFeesPool.sol: L81-86](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L81-L86)

[GiantMevAndFeesPool.sol: L145-146](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L145-L146)

[GiantMevAndFeesPool.sol: L169-170](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L169-L170)

[GiantMevAndFeesPool.sol: L175-176](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L175-L176)

[StakingFundsVault.sol: L45-46](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L45-L46)

[StakingFundsVault.sol: L55-56](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L55-L56)

[StakingFundsVault.sol: L110-113](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L110-L113)

[StakingFundsVault.sol: L197-202](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L197-L202)

[Syndicate.sol: L153-154](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L153-L154)

[LiquidStakingManager.sol: L217-218](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L217-L218)

[LiquidStakingManager.sol: L238-239](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L238-L239)

[LiquidStakingManager.sol: L248-249](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L248-L249)

[LiquidStakingManager.sol: L254-255](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L254-L255)

[LiquidStakingManager.sol: L323-326](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L323-L326)

[LiquidStakingManager.sol: L352-356](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L352-L356)

[LiquidStakingManager.sol: L494-495](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L494-L495)

[LiquidStakingManager.sol: L499-500](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L499-L500)

[LiquidStakingManager.sol: L631-635](https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L631-L635)
___
___

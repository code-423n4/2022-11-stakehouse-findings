## Missing checks for `address(0x0)` when assigning values to `address` state variables

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

```
        liquidStakingManager = ILiquidStakingManager(_manager);
```

>File: contracts/liquid-staking/OptionalHouseGatekeeper.sol [(line 15)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L15)
```
        pool = _pool;
        transferHookProcessor = ITransferHookProcessor(_transferHookProcessor);
```

>File: contracts/liquid-staking/GiantLP.sol [(line 25-26)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L25-L26)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L17


## Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```
        contract LPToken is ILPTokenInit, ILiquidStakingManagerChildContract, Initializable, ERC20PermitUpgradeable {
```
>File: contracts/liquid-staking/LPToken.sol [(line 11)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L11)
```
        contract LiquidStakingManager is ILiquidStakingManager, Initializable, ReentrancyGuard, StakehouseAPI {
```
>File:contracts/liquid-staking/LiquidStakingManager.sol [(line 33)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L33)

```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L36
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L13
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L16
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L16
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L15
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L20-L21

## open `TODO` comments

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

```
            // todo - check else case for any ETH lost
```
* File: contracts/syndicate/Syndicate.sol [(line 195)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L195)

## Use of `block.timestamp`

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers, locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
        lastInteractedTimestamp[_from] = block.timestamp;
        lastInteractedTimestamp[_to] = block.timestamp;
```
>File: contracts/liquid-staking/GiantLP.sol [(line 44-45)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L44-L45)
```
        lastInteractedTimestamp[_from] = block.timestamp;
        lastInteractedTimestamp[_to] = block.timestamp;
```
>File: contracts/liquid-staking/LPToken.sol [(line 76-68)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L67-L68)

```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L96
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L141
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L184
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L230
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L82

## Unused `receive()` function will lock Ether in contract
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```
    receive() external payable {}
```

>File: contracts/liquid-staking/SyndicateRewardsProcessor.sol [(line 98)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98)
```
    receive() external payable {
```

>File: contracts/smart-wallet/OwnableSmartWallet.sol [(line 148)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L148)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L352
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L629

## Set `garbage` value in `mapping` for deleting that

If there is a mapping data structure present inside struct, then deleting the struct doesn't delete the mapping. Instead one should use lock to lock that data structure from further use.


```
            delete smartWalletRepresentative[_smartWallet];
```
>File: /contracts/liquid-staking/LiquidStakingManager.so [(line 713)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L713)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L369
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L621

## Event is missing `indexed` fields

Each event should use three indexed fields if there are three or more fields.

```
    event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
```
>File: contracts/liquid-staking/ETHPoolLPFactory.sol [(line 19)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L19)
```
    event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
```
>File: contracts/liquid-staking/SavETHVault.sol [(line 22)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L22)
```
	Other instances of this issue are:
```
*  https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L121
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L28
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L31
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L63
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L66
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L12
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L19
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L22
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L25
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L19
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L16

## TYPOS
```
    ///@audit: `determins ` 
    /// @notice Utility function that determins whether an LP can be burned for dETH if the associated derivatives have been minted

```
* File: contracts/liquid-staking/SavETHVault.sol [(line 227)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L227)

```
    ///@audit: `admiting ` 
    /// @notice address of optional gatekeeper for admiting new knots to the house created by the network

```
* File: contracts/liquid-staking/LiquidStakingManager.sol [(line 101)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L101)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L476 
///@audit: `validtor` & `initals`
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L903
///@audit: `overriden` 
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L920
///@audit: `overriden` 
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L74
///@audit: `Instane` 

## Consider ordering multiplication first.

Solidity could truncate the results, performing multiplication before division will prevent rounding/truncation in solidity math.
```
                        balance * unprocessedForKnot / (4 ether - currentSlashedAmount);

```
* File: contracts/syndicate/Syndicate.sol [(line 431)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L431)
```
                            balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);

```
* File: contracts/syndicate/Syndicate.sol [(line 522)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L522)

## `public` functions not called by the contract should be declared `external` instead
Contracts are allowed to override their parents’ functions and change the visibility from external to public.
```
    function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
```
* File: contracts/liquid-staking/StakingFundsVault.sol [(line 239)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L239)
```
    function withdrawETHForStaking(
        address _smartWallet,
        uint256 _amount
    ) public onlyManager nonReentrant returns (uint256) {
```
* File: contracts/liquid-staking/SavETHVault.sol [(line 200-203)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L200-L203)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L21-L26
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L73-L78


## `NatSpec` is incomplete

```
    /// @audit Missing: '@return`
    /// @param Missing: '@param' "_deployOptionalHouseGatekeeper" & "_optionalCommission"
    /// @notice Deploys a new LSDN and the liquid staking manger required to manage the network
    /// @param _dao Address of the entity that will govern the liquid staking network
    /// @param _stakehouseTicker Liquid staking derivative network ticker (between 3-5 chars)
    function deployNewLiquidStakingDerivativeNetwork(

```
* File: contracts/liquid-staking/LSDNFactory.sol [(line 70-73)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L70-L73)

```
    /// @param Missing: '@param 
    /// @notice Mints a given amount of LP tokens
    /// @dev Only savETH vault can mint
    function mint(address _recipient, uint256 _amount) external onlyDeployer {

```
* File: contracts/liquid-staking/LPToken.sol [(line 44-46)](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L44-L46)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L33-L34
    /// @param Missing: '@param 

* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L24-L27
    /// @audit Missing: '@return`  &  /// @param Missing: '@param 
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L200-L201ok
    /// @param Missing: '@param 
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L55-L56
    /// @param Missing: '@param 
* https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L217-L228
    /// @audit Missing: '@return`  &  /// @param Missing: '@param 


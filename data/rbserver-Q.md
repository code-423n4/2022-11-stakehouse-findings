## [L-01] BUGS IN SOLIDITY 0.8.13
For this protocol, the used pragmas are 0.8.13 and ^0.8.13, and the hardhat configuration uses 0.8.13. Yet, Solidity 0.8.13 has a size check bug, which involves nested calldata array and `abi.encode`, and an optimizer bug that affects inline assembly. It looks like that the code in scope are not affected by these bugs but the protocol team should be aware of them.

Please see the following links for reference:
- https://blog.soliditylang.org/2022/05/17/calldata-reencode-size-check-bug/
- https://blog.soliditylang.org/2022/06/15/inline-assembly-memory-side-effects-bug/

Please consider using at least Solidity 0.8.15 instead of 0.8.13 to address these bugs and be more future-proofed.

The following files use 0.8.13.
```solidity
contracts\syndicate\Syndicate.sol
  1: pragma solidity 0.8.13;

contracts\syndicate\SyndicateErrors.sol
  1: pragma solidity 0.8.13;

contracts\syndicate\SyndicateFactory.sol
  1: pragma solidity 0.8.13;
```

The current hardhat configuration is shown below.
https://github.com/code-423n4/2022-11-stakehouse/blob/main/hardhat.config.js#L4-L14
```typescript
module.exports = {
  solidity: {
    version: "0.8.13",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200
      }
    }
  }
};
```

## [L-02] MISSING `address(0)` CHECKS FOR CRITICAL ADDRESS INPUTS
To prevent unintended behaviors, critical address inputs should be checked against `address(0)`.

`address(0)` checks are missing for `_savETHVaultDeployer`, `_stakingFundsVaultDeployer`, and `_optionalGatekeeperDeployer` in the following `_init` function. Please consider checking these.
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L645-L679
```solidity
    function _init(
        address _dao,
        address _syndicateFactory,
        address _smartWalletFactory,
        address _lpTokenFactory,
        address _brand,
        address _savETHVaultDeployer,
        address _stakingFundsVaultDeployer,
        address _optionalGatekeeperDeployer,
        uint256 _optionalCommission,
        bool _deployOptionalGatekeeper,
        string calldata _stakehouseTicker
    ) internal {
        ...

        _initStakingFundsVault(_stakingFundsVaultDeployer, _lpTokenFactory);
        _initSavETHVault(_savETHVaultDeployer, _lpTokenFactory);

        if (_deployOptionalGatekeeper) {
            gatekeeper = OptionalGatekeeperFactory(_optionalGatekeeperDeployer).deploy(address(this));
        }
    }
```

## [L-03] INCORRECT COMMENT
Incorrect comment can be misleading. The following `require` statement acts as the opposite of its comment. For better readability and maintainability, please consider updating the comment to match the code.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L468-L469
```solidity
            // check if the BLS public key is part of LSD network and is not banned
            require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
```

## [L-04] INACCURATE `require` REASONS
Inaccurate reason strings for `require` statements can be misleading. The following `require` statements' reason strings do not describe the `require` statements' conditions accurately. Please consider updating these reason strings to be more accurate and descriptive.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L468-L469
```solidity
            // check if the BLS public key is part of LSD network and is not banned
            require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");
```

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L175
```solidity
        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
```

## [N-01] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, constants can be used instead of magic numbers. Please consider replacing the following magic numbers, such as `4 ether`, with constants.

```solidity
contracts\liquid-staking\LiquidStakingManager.sol
  333: require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");  
  343: 4 ether  ; mi: extra amt left that is less than 4 ether cannot be withdrawn
  434: require(msg.value == len * 4 ether, "Insufficient ether provided"); 
  640: 1   
  749: savETHVault.withdrawETHForStaking(smartWallet, 24 ether);
  752: stakingFundsVault.withdrawETH(smartWallet, 4 ether);   
  766: 32 ether   
  870: sETH.approve(syndicate, (2 ** 256) - 1);    
  882: uint256 stakeAmount = 12 ether; 
  936: require(associatedSmartWallet.balance >= 4 ether, "Smart wallet balance must be at least 4 ether"); 
  940: require(stakingFundsLP.totalSupply() == 4 ether, "DAO staking funds vault balance must be at least 4 ether");
  944: require(savETHVaultLP.totalSupply() == 24 ether, "KNOT must have 24 ETH in savETH vault");

contracts\liquid-staking\SavETHVault.sol
  161: require(dETHBalance >= 24 ether, "Nothing to withdraw");    
  174: redemptionValue = (dETHDetails.savETHBalance * _amount) / 24 ether;    
  204: require(_amount >= 24 ether, "Amount cannot be less than 24 ether");    
  244: maxStakingAmountPerValidator = 24 ether;    

contracts\liquid-staking\StakingFundsVault.sol
  58: totalShares += 4 ether; 
  184: require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");    
  230: require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");  
  240: require(_amount >= 4 ether, "Amount cannot be less than 4 ether");  
  380: maxStakingAmountPerValidator = 4 ether; 

contracts\syndicate\Syndicate.sol
  215: if (_sETHAmount < 1 gwei) revert FreeFloatingStakeAmountTooSmall(); 
  221: if (totalStaked == 12 ether) revert KnotIsFullyStakedWithFreeFloatingSlotTokens();  
  223: if (_sETHAmount + totalStaked > 12 ether) revert InvalidStakeAmount();  
  362: if (stakedBal < 1 gwei) revert FreeFloatingStakeAmountTooSmall();  
  504: if (currentSlashedAmount < 4 ether) {  
  522: balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);
```

## [N-02] MISSING INDEXED EVENT FIELDS
Querying event can be optimized with index. Please consider adding `indexed` to the relevant fields of the following events.

```solidity
contracts\liquid-staking\LiquidStakingManager.sol
  84: event DAOCommissionUpdated(uint256 old, uint256 newCommission); 

contracts\liquid-staking\SavETHVault.sol
  19: event DETHRedeemed(address depositor, uint256 amount);  
  22: event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);  

contracts\liquid-staking\StakingFundsVault.sol
  25: event ETHDeposited(address sender, uint256 amount); 
  28: event ETHWithdrawn(address receiver, address admin, uint256 amount); 
  31: event ERC20Recovered(address admin, address recipient, uint256 amount); 
  34: event WETHUnwrapped(address admin, uint256 amount); 
```

## [N-03] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.

```solidity
contracts\liquid-staking\LiquidStakingManager.sol
  911: function _initStakingFundsVault(address _stakingFundsVaultDeployer, address _tokenFactory) internal virtual {   

contracts\liquid-staking\SavETHVault.sol
  45: function init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) external virtual initializer {  

contracts\liquid-staking\SavETHVaultDeployer.sol
  18: function deploySavETHVault(address _liquidStakingManger, address _lpTokenFactory) external returns (address) {  

contracts\liquid-staking\StakingFundsVaultDeployer.sol
  18: function deployStakingFundsVault(address _liquidStakingManager, address _tokenFactory) external returns (address) { 

contracts\smart-wallet\OwnableSmartWalletFactory.sol
  28: function createWallet() external returns (address wallet) { 
  32: function createWallet(address owner) external returns (address wallet) { 
  36: function _createWallet(address owner) internal returns (address wallet) { 

contracts\syndicate\Syndicate.sol
  491: function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {   
```
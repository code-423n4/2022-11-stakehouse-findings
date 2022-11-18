### G-01 There is no need for constants to be public

Change accessibility of all constants in the code from `public` to `private` to save gas

### G-02 Use custom errors instead of require statements with error messages

Custom errors have recently been added to the Solidity language and usually result in big gas cost savings on errors.

### G-03 Always wrap `++i` in `unchecked{}` in a for loop

Since all for loops have an end condition that is keeping `i` from overflowing, you can wrap it in an `unchecked{}` block to save gas

### G-04 Always use `++x` instead of `x++`

Code occurrence: `numberOfLPTokensIssued**++**;` change it to `++numberOfLPTokensIssued` to save gas

### G-05 Make public methods external so their arguments are of type `calldata`

1. [SyndicateFactory::deploySyndicate](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/SyndicateFactory.sol#L21)
2. [GiantPoolBase::depositETH](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L34)
3. [OwnableSmartWallet::transferOwnership](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/smart-wallet/OwnableSmartWallet.sol#L94)

### G-06 Reuse local variable in event instead of going to storage

In ETHPoolLPFactory::rotateLPTokens we have defined `bytes memory blsPublicKeyOfNewKnot **=** KnotAssociatedWithLPToken[_newLPToken];` but in the event emission we have **`emit** LPTokenMinted(KnotAssociatedWithLPToken[_newLPToken], address(_newLPToken), **msg.sender**, _amount);` - use the `blsPublicKeyOfNewKnot` variable as first event argument to save gas and skip going to storage

### G-07 Use `--x` instead of `x -= 1` and `++x` instead of `x += 1`

In Syndicate::_deRegisterKnot we have `numberOfRegisteredKnots **-=** *1*;` - change it to `--numberOfRegisteredKnots` instead to save gas. Also we have `numberOfKnots **+=** *1` in LiquidStakingManager* line 839 and 782. Change to `++numberOfKnots;`

### G-08 Move require statements to be before complex computations

In [SavETHVault::burnLPToken](https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L186) we define a flag `isStaleLiquidty` but check if it is valid only after going through a big if statement. Instead, to save gas, check the flag exactly after defining/computing it to fail fast and save gas in case of error.

### G-09 Use `x = x - y` instead of `x -= y` to save gas, same for addition

Replace all occurrences of `x = x - y` to `x -= y` to save gas, and do the same for addition.
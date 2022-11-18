
# GAS

## Increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks


https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L392
        for(uint256 i; i < _blsPubKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L465
        for(uint256 i; i < len; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L76
        for (uint256 i; i < amountOfTokens; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L42
        for (uint256 i; i < numOfSavETHVaults; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L78
        for (uint256 i; i < numOfVaults; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L82
            for (uint256 j; j < _lpTokens[i].length; ++j) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L128
        for (uint256 i; i < numOfRotations; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L146
        for (uint256 i; i < numOfVaults; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L148
            for (uint256 j; j < _lpTokens[i].length; ++j) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L63
        for (uint256 i; i < numOfValidators; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L103
        for (uint256 i; i < numOfTokens; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L116
        for (uint256 i; i < numOfTokens; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L38
        for (uint256 i; i < numOfVaults; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L64
        for (uint256 i; i < numOfVaults; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L90
        for (uint256 i; i < _stakingFundsVaults.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L117
        for (uint256 i; i < numOfRotations; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L135
        for (uint256 i; i < numOfVaults; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L183
        for (uint256 i; i < _lpTokens.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L78
        for (uint256 i; i < numOfValidators; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L152
        for (uint256 i; i < numOfTokens; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L166
        for (uint256 i; i < numOfTokens; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L203
        for (uint256 i; i < _blsPubKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L266
        for (uint256 i; i < _blsPublicKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L276
        for (uint256 i; i < _blsPubKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L286
        for (uint256 i; i < _token.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L211
        for (uint256 i; i < _blsPubKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L259
        for (uint256 i; i < _blsPubKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L301
        for (uint256 i; i < _blsPubKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L346
        for (uint256 i; i < _blsPubKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L420
        for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L513
                    for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L560
        for (uint256 i; i < knotsToRegister; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L585
        for (uint256 i; i < _priorityStakers.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L598
        for (uint256 i; i < _blsPublicKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L648
        for (uint256 i; i < _blsPubKeys.length; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L538
        for (uint256 i; i < numOfValidators; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L587
        for (uint256 i; i < numOfKnotsToProcess; ++i) {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L63
        for (uint256 i; i < numOfRotations; ++i) {


### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header




## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L123
    mapping(address => bool) public isNodeRunnerWhitelisted;

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L126
    mapping(address => address) public smartWalletRepresentative;
- - - - -
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L132
    mapping(address => address) public smartWalletOfNodeRunner;

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L135
    mapping(address => address) public nodeRunnerOfSmartWallet;

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L138
    mapping(address => uint256) public stakedKnotsOfSmartWallet;

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L141
    mapping(address => address) public smartWalletDormantRepresentative;

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L149
    mapping(address => bool) public bannedNodeRunners;


### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.


## `abi.encode()` is less gas efficient than `abi.encodePacked()`
### Summary
In general, `abi.encodePacked` is more gas-efficient.

### Details
Changing the abi.encode function to `abi.encodePacked` can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/SyndicateFactory.sol#L63
        return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));

### Mitigation
Consider changing abi.encode to `abi.encodePacked`



## splitting `require()` statements that use `&&` saves gas
### Summary
Instead of using the && operator in a single require statement to check multiple conditions, consider using multiple require statements with 1 condition per require statement (saving 3 gas per & ):
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L357
        require(_new != address(0) && _current != _new, "New is zero or current");


### Mitigation
Split require statements

## Functions guaranteed to revert when called by normal users can be marked `payable`
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/smart-wallet/OwnableSmartWallet.sol#L114
    function setApproval(address to, bool status) external onlyOwner override {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L147
    ) external onlyOwner {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L154
    function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L161
    function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L168
    function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {

### Mitigation
Consider adding payable to save gas



## Unnecesary storage read
### Summary
A value which value is already known can be used directly rather than reading it from the storage
### Example
```
        bytes memory blsPublicKeyOfNewKnot = KnotAssociatedWithLPToken[_newLPToken];

        require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");
        require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");

        require(
            getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfPreviousKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
            "Lifecycle status must be one"
        );

        require(
            getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
            "Lifecycle status must be one"
        );

        // burn old tokens and mint new ones
        _oldLPToken.burn(msg.sender, _amount);
        emit LPTokenBurnt(blsPublicKeyOfPreviousKnot, address(_oldLPToken), msg.sender, _amount);

        _newLPToken.mint(msg.sender, _amount);
        emit LPTokenMinted(KnotAssociatedWithLPToken[_newLPToken], address(_newLPToken), msg.sender, _amount);
    }
```

The value is already known, so it can be avoided to read it again

Recommendation
Change to:

```
        bytes memory blsPublicKeyOfNewKnot = KnotAssociatedWithLPToken[_newLPToken]; //@audit value already known

        require(blsPublicKeyOfPreviousKnot.length == 48, "Incorrect BLS public key");
        require(blsPublicKeyOfNewKnot.length == 48, "Incorrect BLS public key");

        require(
            getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfPreviousKnot) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
            "Lifecycle status must be one"
        );

        require(
            getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
            "Lifecycle status must be one"
        );

        // burn old tokens and mint new ones
        _oldLPToken.burn(msg.sender, _amount);
        emit LPTokenBurnt(blsPublicKeyOfPreviousKnot, address(_oldLPToken), msg.sender, _amount);

        _newLPToken.mint(msg.sender, _amount);
        emit LPTokenMinted(blsPublicKeyOfNewKnot, address(_newLPToken), msg.sender, _amount);//@audit don't read it again
    }
```
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/a0558ed7b12e1ace1fe5c07970c7fc07eb00eebd/contracts/liquid-staking/ETHPoolLPFactory.sol#L85-L107

### Mitigation
Set directly the value to avoid unnecessary storage read to save some gas



## Internal functions only called once can be inlined to save gas
### Summary
Not inlining costs `20` to `40` gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L597
    function _deRegisterKnots(bytes[] calldata _blsPublicKeys) internal {

### Mitigation
Consider changing internal function only called once to inline code for gas savings


## Variables should be cached when used several times
### Summary
Variables read more than once improves gas usage when cached into local variable
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L204-L213
`_blsPubKeys[i]`
```
            require(
                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
                "Unknown BLS public key"
            );

            // Ensure that the BLS key has its derivatives minted
            require(
                getAccountManager().blsPublicKeyToLifecycleStatus(_blsPubKeys[i]) == IDataStructures.LifecycleStatus.TOKENS_MINTED,
                "Derivatives not minted"
            );
```
### Mitigation
Cache variables used more than one into a local variable.






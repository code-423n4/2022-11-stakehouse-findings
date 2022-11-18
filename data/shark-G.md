## Unnecessary boolean literal compare

You will save gas on deployment by not comparing boolean literals (i.e. `true` and `false`)

For instance:

File: `LiquidStakingManager.sol` [Line 436](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L436)

```
        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");
```

In the code above, the `== true` could be omitted:

```
        require(_isNodeRunnerValid(msg.sender), "Unrecognised node runner");
```

For checking if a condition is falsy:

File: `LiquidStakingManager.sol` [Line 437](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L437)

```
        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");
```

Use the `!` operator to negate a condition instead:

```
        require(!isNodeRunnerBanned(msg.sender), "Node runner is banned from LSD network");
```

HEre are a few other instances:

File: `LiquidStakingManager.sol` [Line 291](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L291), [Line 328](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L328), [Line 332](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L332), [Line 393](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L393), [Line 436](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L436), [Line 437](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L437), [Line 469](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L469), [Line 541](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L541), [Line 589](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L589), [Line 688](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L688)

```
291:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

328:        require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

332:        require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

393:            require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

436:        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

437:        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

469:            require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

541:            require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

589:            require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

688:            require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
```

## Missing `constant` type

If you are not going to be modifying the variable, you can set it to `constant` to save gas.

File: `LiquidStakingManager.sol` [Line 158](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158)

```
    uint256 public MODULO = 100_00000;
```

The variable above is in uppercase, but is missing `constant`.

Consider refactoring to:

```
    uint256 public constant MODULO = 100_00000;
```

## `abi.encode()` costs more gas than `abi.encodePacked()`

The reason `abi.encode()` isn't as gas-efficient is because it pads extra null bytes at the end of the calldata while `abi.encodePacked()` does not.

For more info visit [this repo](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison)

Here is an example:

File: `SyndicateFactory.sol` [Line 63](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L63)

```
        return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));
```

The above can be changed to:

```
        return keccak256(abi.encodePacked(_deployer, _contractOwner, _numberOfInitialKnots));
```

## Use `bytes32` instead of `string`

You only want to use `string` for dynamically allocated data, otherwise `bytes32` will cost less gas.

For example:

File: `LPTokenFactory.sol` [Line 31](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L31)

```
        string calldata _tokenName
```

The above could be changed to type `bytes32`:

```
        bytes32 calldata _tokenName
```

Here are some more instances of this issue:

File:`LiquidStakingManager.sol` [Line 54](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L54), [Line 69](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L69), [Line 111](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L111), [Line 180](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L180), [Line 255](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L255)

```
54:    event StakehouseCreated(string stakehouseTicker, address indexed stakehouse);

69:    event NetworkTickerUpdated(string newTicker);

111:    string public stakehouseTicker;

180:        string calldata _stakehouseTicker

255:    function updateTicker(string calldata _newTicker) external onlyDAO {
```

## QA

### Missing checks for address(0x0) when assigning values to address state variables
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L25
```solidity
File: /contracts/liquid-staking/GiantLP.sol
25:        pool = _pool;
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/SyndicateFactory.sol#L17
```solidity
File: /contracts/syndicate/SyndicateFactory.sol
17:        syndicateImplementation = _syndicateImpl;
```
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L15
```solidity
File: /contracts/liquid-staking/OptionalHouseGatekeeper.sol
15:        liquidStakingManager = ILiquidStakingManager(_manager);
```
### Public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.
Functions marked by external use call data to read arguments, where public will first allocate in local memory and then load them.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L514-L516
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
514:    function isKnotDeregistered(bytes calldata _blsPublicKey) public view returns (bool) {
515:        return Syndicate(payable(syndicate)).isNoLongerPartOfSyndicate(_blsPublicKey);
516:    }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L458-L461
```solidity
File: /contracts/syndicate/Syndicate.sol
458:    function calculateNewAccumulatedETHPerCollateralizedSharePerKnot() public view returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L113
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
113:    function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LSDNFactory.sol#L73-L78
```solidity
File: /contracts/liquid-staking/LSDNFactory.sol
73:    function deployNewLiquidStakingDerivativeNetwork(
74:        address _dao,
75:        uint256 _optionalCommission,
76:        bool _deployOptionalHouseGatekeeper,
77:        string calldata _stakehouseTicker
78:    ) public returns (address) {
```

### Internal function not called by the contract should be removed
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L538-L542
```solidity
File: /contracts/syndicate/Syndicate.sol

538:    function _calculateCollateralizedETHOwedPerKnot() internal view returns (uint256) {


545:    function _calculateNewAccumulatedETHPerCollateralizedShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) {
```

### Typos
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L227
```solidity
File: /contracts/liquid-staking/SavETHVault.sol

//@audit: determins instead of determines
227:    /// @notice Utility function that determins whether an LP can be burned for dETH if the associated derivatives have been minted
```

### Open todos
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L195
```solidity
File: /contracts/syndicate/Syndicate.sol
195:            // todo - check else case for any ETH lost
```
### `2**<n> - 1` should be re-written as `type(uint<n>).max`

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L870
```solidity
File: /contracts/liquid-staking/LiquidStakingManager.sol
870:        sETH.approve(syndicate, (2 ** 256) - 1);
```

```diff
diff --git a/contracts/liquid-staking/LiquidStakingManager.sol b/contracts/liquid-staking/LiquidStakingManager.sol
index 8d4a8fa..f72c726 100644
--- a/contracts/liquid-staking/LiquidStakingManager.sol
+++ b/contracts/liquid-staking/LiquidStakingManager.sol
@@ -867,7 +867,7 @@ contract LiquidStakingManager is ILiquidStakingManager, Initializable, Reentranc
         );

         // Contract approves syndicate to take sETH on behalf of the DAO
-        sETH.approve(syndicate, (2 ** 256) - 1);
+        sETH.approve(syndicate, type(uint256).max);

         // Auto-stake sETH by pulling sETH out the smart wallet and staking in the syndicate
         _autoStakeWithSyndicate(associatedSmartWallet, _blsPublicKeyOfKnot);
```

### Lack of event emission after critical initialize() functions
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L45-L47
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
45:    function init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) external virtual initializer {
46:        _init(_liquidStakingManagerAddress, _lpTokenFactory);
47:    }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWallet.sol#L28-L38
```solidity
File: /contracts/smart-wallet/OwnableSmartWallet.sol
28:    function initialize(address initialOwner)

38:    }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LPToken.sol#L32-L42
```solidity
File: /contracts/liquid-staking/LPToken.sol
32:    function init(
33:        address _deployer,
34:        address _transferHookProcessor,
35:        string calldata _tokenSymbol,
36:        string calldata _tokenName
37:    ) external override initializer {
38:        deployer = _deployer;
39:        transferHookProcessor = ITransferHookProcessor(_transferHookProcessor);
40:        __ERC20_init(_tokenName, _tokenSymbol);
41:        __ERC20Permit_init(_tokenName);
42:    }
```
### The nonReentrant modifier should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L255-L259
```solidity
File: /contracts/liquid-staking/StakingFundsVault.sol
255:    function unstakeSyndicateSharesForRageQuit(
256:        address _sETHRecipient,
257:        bytes[] calldata _blsPublicKeys,
258:        uint256[] calldata _amounts
259:    ) external onlyManager nonReentrant {
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L200-L203
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
200:    function withdrawETHForStaking(
201:        address _smartWallet,
202:        uint256 _amount
203:    ) public onlyManager nonReentrant returns (uint256) {
```

### Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L19-L22
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
19:    event DETHRedeemed(address depositor, uint256 amount);

22:    event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
```
### Code Structure Deviates From Best-Practice
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier. Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Some constructs deviate from this recommended best-practice: Modifiers are in the middle of contracts. External/public functions are mixed with internal/private ones. Few events are declared in contracts while most others are in corresponding interfaces.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SavETHVault.sol#L121
```solidity
File: /contracts/liquid-staking/SavETHVault.sol
121:    event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

The above event is declared in the middle of the contract while other events were declared at the top

 **Recommendation:**
Consider adopting recommended best-practice for code structure and layout.

See: [https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout)

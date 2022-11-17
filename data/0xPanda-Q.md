# UNUSED EVENT
contracts/liquid-staking/SavETHVault.sol:121
```
event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
```

# MISSING SPACE
contracts/liquid-staking/ETHPoolLPFactory.sol:97
missing space after `==`
`==IDataStructures`
```
require(
            getAccountManager().blsPublicKeyToLifecycleStatus(blsPublicKeyOfNewKnot) ==IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
            "Lifecycle status must be one"
        );
```


contracts/liquid-staking/LiquidStakingManager.sol:392,465
missing space after `for`

```
//392
for(uint256 i; i < _blsPubKeys.length; ++i) {
//465
for(uint256 i; i < len; ++i) {
```

# INCORRECT COMMENT

contracts/liquid-staking/GiantSavETHVaultPool.sol:100,101

```
// Ask
vault.burnLPTokens(_lpTokens[i], _amounts[i]);
```

# Require - always false
contracts/liquid-staking/LiquidStakingManager.sol:280
`isNodeRunnerWhitelisted[_nodeRunner]` comparing to itself
```
require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
```

should be 
```
require(isNodeRunnerWhitelisted[_nodeRunner] != isWhitelisted, "Unnecessary update to same status");
```

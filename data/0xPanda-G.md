# USE UNCHECKED i IN FOR LOOP

Each time i++ is called under/overflow checks are made.
But we're already constraining i by length, `i < length`, making those under/overflow checks unnecessary. 

contracts/liquid-staking/ETHPoolLPFactory.sol:63
contracts/liquid-staking/GiantMevAndFeesPool.sol:38,64,90,117,135,183
contracts/liquid-staking/GiantPoolBase.sol:76
contracts/liquid-staking/GiantSavETHVaultPool.sol:42,78,82,128,146,148
contracts/liquid-staking/LiquidStakingManager.sol:538,587
contracts/liquid-staking/SavETHVault.sol:63,103,116
contracts/liquid-staking/StakingFundsVault.sol:78,152,166,203,266,276,286
contracts/syndicate/Syndicate.sol:211,259,301,346,420,513,560,585,598,648

suggestion - use the format below 

```
for (uint256 i; i < length;) {
  ...
  unchecked{ ++i; }
}
```


# STATE VARIABLES SHOULD BE CACHED IN STACK VARIABLES RATHER THAN RE-READING THEM FROM STORAGE

contracts/liquid-staking/GiantSavETHVaultPool.sol:77,105,108
should cache the result of `getDETH()`

contracts/liquid-staking/StakingFundsVault.sol line 79,81,85,99,102
`_blsPublicKeyOfKnots[i]`

contracts/liquid-staking/SavETHVault.sol: 158,159,165,177,179
`getSavETHRegistry()`

contracts/liquid-staking/SavETHVault.sol:64,66,72
`_blsPublicKeyOfKnots[i]`

contracts/liquid-staking/GiantMevAndFeesPool.sol:40,48
`_ETHTransactionAmounts[i]`

contracts/liquid-staking/GiantMevAndFeesPool.sol:184
`_lpTokens[i]`

contracts/liquid-staking/StakingFundsVault.sol:79,81,85,99,102
`_blsPublicKeyOfKnots[i]`

contracts/liquid-staking/StakingFundsVault.sol:205,211215,228
`_blsPubKeys[i]`

contracts/syndicate/Syndicate.sol:415,416,421,423
`getSlotRegistry()`

contracts/syndicate/Syndicate.sol:501,506,510,514,515
`getSlotRegistry()`

contracts/syndicate/Syndicate.sol:573,575
`getSlotRegistry()`

contracts/liquid-staking/LiquidStakingManager.sol:296,299
`smartWalletRepresentative[smartWallet]`

contracts/liquid-staking/LiquidStakingManager.sol:314,317
`smartWalletRepresentative[smartWallet]`

contracts/liquid-staking/LiquidStakingManager.sol:393,396
`_blsPubKeys[i]`

contracts/liquid-staking/LiquidStakingManager.sol:589,593,600,608,614
`_blsPublicKeyOfKnots`

# NOT USED CACHE LOCAL VARIABLE

ARRAY LENGTH NOT USED IN FOR LOOP
contracts/syndicate/Syndicate.sol:343
`_blsPubKeys.length` is used in stead of local variable `numOfKeys`
```
function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {
	uint256 numOfKeys = _blsPubKeys.length;
	if (numOfKeys == 0) revert EmptyArray();
	for (uint256 i; i < _blsPubKeys.length; ++i) {
		_updateCollateralizedSlotOwnersLiabilitySnapshot(_blsPubKeys[i]);
	}
}
```

contracts/liquid-staking/LiquidStakingManager.sol: 554
`_blsPublicKeyOfKnots[i]` is  used instead of the local variable  `blsPubKey` in line 539
```
for (uint256 i; i < numOfValidators; ++i) {
            bytes calldata blsPubKey = _blsPublicKeyOfKnots[i];
            // check if BLS public key is registered with liquid staking derivative network and not banned
            require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

            address associatedSmartWallet = smartWalletOfKnot[blsPubKey];
            require(associatedSmartWallet != address(0), "Unknown BLS public key");
            require(
                getAccountManager().blsPublicKeyToLifecycleStatus(blsPubKey) == IDataStructures.LifecycleStatus.INITIALS_REGISTERED,
                "Initials not registered"
            );

            // check minimum balance of smart wallet, dao staking fund vault and savETH vault
            _assertEtherIsReadyForValidatorStaking(blsPubKey);

            _stake(
                _blsPublicKeyOfKnots[i],
                _ciphertexts[i],
                _aesEncryptorKeys[i],
                _encryptionSignatures[i],
                _dataRoots[i]
            );

            ...

```

# <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
(also include <X> -= <Y>)

contracts/liquid-staking/GiantMevAndFeesPool.sol:40
contracts/liquid-staking/GiantPoolBase.sol:39,57
contracts/liquid-staking/GiantSavETHVaultPool.sol:46
contracts/liquid-staking/LiquidStakingManager.sol:615,770,782,839
contracts/liquid-staking/SavETHVault.sol:71
contracts/liquid-staking/StakingFundsVault.sol:58,97,278,287
contracts/liquid-staking/SyndicateRewardsProcessor.sol:65
contracts/syndicate/Syndicate.sol:185,190,225,226,227,269,272,273,317,430,511,521,558,621,624,658

# DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

contracts/liquid-staking/GiantSavETHVaultPool.sol:150
contracts/liquid-staking/LiquidStakingManager.sol:291,328,332,393,436,437,469,541,589,688
contracts/liquid-staking/SavETHVault.sol:64,84
contracts/liquid-staking/StakingFundsVault.sol:79,114,205
contracts/syndicate/Syndicate.sol:611,612

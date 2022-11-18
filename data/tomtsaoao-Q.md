# 

[01] Outdated Solidity pragma versioning

Solidity pragma versioning should be upgraded to latest available version. Currently the solidity version in contracts is ^0.8.13 which was found to possess some bugs.

[02] Mismatching Solidity pragma versioning

Solidity pragma versioning should be exactly same in all contracts. Currently some contracts use ^0.8.13 but some are fixed to 0.8.13.

E.g: 

`contracts/syndicate/Syndicate.sol` - 0.8.13
`contracts/liquid-staking/ETHPoolLPFactory.sol` - ^0.8.13

[03] Unused functions

`Syndicate._calculateCollateralizedETHOwedPerKnot()` is never used and should be removed

[https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/syndicate/Syndicate.sol#L538](https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/syndicate/Syndicate.sol#L538)

`Syndicate._calculateNewAccumulatedETHPerCollateralizedShare(uint256)` is never used and should be removed

[https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/syndicate/Syndicate.sol#L544](https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/syndicate/Syndicate.sol#L544)

[04] Missing zero-checks

`GiantLP.constructor(address,address,string,string)` lacks an address-zero check on `_pool`

[https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/GiantLP.sol#L19](https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/GiantLP.sol#L19)

`LPToken.init(address,address,string,string)` lacks an address-zero check on `deployer`

[https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/LPToken.sol#L32](https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/LPToken.sol#L32)

`OwnableSmartWallet.rawExecute` lacks an address-zero check on `target`

[https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/smart-wallet/OwnableSmartWallet.sol#L67](https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/smart-wallet/OwnableSmartWallet.sol#L67)
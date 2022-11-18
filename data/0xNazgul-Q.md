## [NAZ-L1] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`LiquidStakingManager.sol#L255`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L255)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L2] Missing Zero-address Validation
**Severity**: Low
**Context**: [`LPToken.sol#L32`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L32), [`OwnableSmartWallet.sol#L28`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L28), [`SavETHVault.sol#L45`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L45)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-L3] Lack of Event Emission For Critical Functions
**Severity**: Low
**Context**: [`Syndicate.sol#L168`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L168), [`StakingFundsVault.sol#L56`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L56)

**Description**:
Several functions update critical parameters that are missing event emission. These should be performed to ensure tracking of changes of such critical parameters.

**Recommendation**:
Consider adding events to functions that change critical parameters.


## [NAZ-L4] `receive()` Function Should Emit An Event
**Severity**: Low
**Context**: [`OwnableSmartWallet.sol#L148`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L148), [`Syndicate.sol#L352`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L352), [`LiquidStakingManager.sol#L629`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L629), [`SyndicateRewardsProcessor.sol#L98`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98)

**Description**:
Consider emitting an event inside this function with `msg.sender` and `msg.value` as the parameters. This would make it easier to track incoming ether transfers.

**Recommendation**:
Consider adding events to the `receive()` functions. 


## [NAZ-N1] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`LiquidStakingManager.sol#L158`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L158), [`ETHPoolLPFactory.sol#L28-L29`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L28-L29)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N2] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`SavETHVaultDeployer.sol#L13`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L13), [`StakingFundsVaultDeployer.sol#L13`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L13), [`LPTokenFactory.sol#L15`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15), [`SyndicateFactory.sol#L48`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L48), [`GiantPoolBase.sol#L22`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L22), [`LSDNFactory.sol#L15`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L15), [`GiantSavETHVaultPool.sol#L66`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L66), [`OwnableSmartWallet.sol#L94`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L94), [`SavETHVault.sol#L25`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L25), [`SavETHVault.sol#L121`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L121), [`GiantMevAndFeesPool.sol#L105`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L105), [`StakingFundsVault.sol#L37`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L37), [`Syndicate.sol#L66`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L66), [`LiquidStakingManager.sol#L90`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L90), [`SyndicateRewardsProcessor.sol#L15`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L15), [`ETHPoolLPFactory.sol#L28-L29`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L28-L29)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N3] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`LPToken.sol#L28`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L28), [`GiantPoolBase.sol#L100-L103`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L100-L103), [`OwnableSmartWallet.sol#L25`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L25), [`SavETHVault.sol#L43`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L43), [`StakingFundsVault.sol#L43`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L43), [`Syndicate.sol#L123`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L123), [`LiquidStakingManager.sol#L166`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L166), [`LiquidStakingManager.sol#L629`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L629), [`SyndicateRewardsProcessor.sol#L98`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L98)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N4] Unindexed Event Parameters
**Severity** Informational
**Context**: [`SavETHVault.sol#L121`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L121), [`StakingFundsVault.sol#L25-L34`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L25-L34), [`Syndicate.sol#L57-L60`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L57-L60), [`ETHPoolLPFactory.sol#L16-L25`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L16-L25)

**Description**:
Parameters of certain events are expected to be indexed so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.

**Recommendation**:
Consider adding the indexed keyword to event parameters that should include it.


## [NAZ-N5] TODOs Left In The Code
**Severity**: Informational
**Context**: [`Syndicate.sol#L195`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L195)

**Description**:
There should never be any TODOs in the code when deploying.

**Recommendation**:
Consider finishing the TODOs before deploying.


## [NAZ-N6] SPDX License Identifier In Wrong Spot
**Severity**: Informational
**Context**: [`OptionalHouseGatekeeper.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol), [`SavETHVaultDeployer.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol), [`LPTokenFactory.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol), [`GiantLP.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol), [`LPToken.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol), [`SyndicateFactory.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol), [`GiantPoolBase.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol), [`LSDNFactory.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol), [`GiantSavETHVaultPool.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol), [`GiantMevAndFeesPool.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol), [`Syndicate.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol), [`SyndicateErrors.sol`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateErrors.sol)

**Description**:
SPDX license identifier is in the wrong spot in the source file. Before publishing.

**Recommendation**: 
Consider moving the "SPDX-License-Identifier: <SPDX-License>" before the declaration of the pragma version.


## [NAZ-N7] Spelling Errors
**Severity**: Informational
**Context**: [`SavETHVault.sol#L115 (Inconsisent => Inconsistent)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L115), [`SavETHVault.sol#L227 (determins => determines)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L227), [`Syndicate.sol#L356 (publice => public)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L356), [`Syndicate.sol#L490 (amongs => among)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L490), [`Syndicate.sol#L565 (incomming => incoming)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L565), [`Syndicate.sol#L490 (funtion => function)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L490), [`LiquidStakingManager.sol#L51 (trigerringAddress => triggeringAddress)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L51), [`LiquidStakingManager.sol#L101 (admiting => admitting)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L101), [`LiquidStakingManager.sol#L476 (validtor => validator)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L476), [`LiquidStakingManager.sol#L476 (initals => initials)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L476), [`LiquidStakingManager.sol#L920 (customise => customize)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L920), [`ETHPoolLPFactory.sol#L74 (Instane => Instance)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L74), [`ETHPoolLPFactory.sol#L124 (depoistor => depositor)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L124), [`ETHPoolLPFactory.sol#L150 (depoistor => depositor)`](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/ETHPoolLPFactory.sol#L150)

**Description**:
Spelling errors in comments can cause confusion to both users and developers.

**Recommendation**:
Consider checking all misspellings to ensure they are corrected.


## [NAZ-N8] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N9] Floating Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts)

**Description**:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

**Recommendation**: 
Consider locking the pragma version.


## [NAZ-N10] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-11-stakehouse/tree/main/contracts)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.

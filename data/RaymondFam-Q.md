## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are the contract instances missing NatSpec in its entirety:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol

Here are the function instances missing full or partial set of `@param`s:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L19

```
    function isMemberPermitted(bytes calldata _blsPublicKeyOfKnot) external override view returns (bool) {
```
[File: OwnableSmartWalletFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol)
```
32:    function createWallet(address owner) external returns (address wallet) {

36:    function _createWallet(address owner) internal returns (address wallet) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L27

```
    function deployLPToken(
```
[File: GiantLP.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol)

```
29:    function mint(address _recipient, uint256 _amount) external {

34:    function burn(address _recipient, uint256 _amount) external {

39:    function _beforeTokenTransfer(address _from, address _to, uint256 _amount) internal override {

43:    function _afterTokenTransfer(address _from, address _to, uint256 _amount) internal override {
```
## Unspecific Compiler Version Pragma
For most source-units the compiler version pragma is very unspecific ^0.8.13. While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different EVM compilation that is ultimately deployed on the blockchain.

Avoid floating pragmas where possible. It is highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.

## Sanity/Threshold/Limit Checks
Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider implementing a complete set of zero address/uint and/or empty string/bytes checks in the constructor. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors entailing immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values.

As an example, the following code block needing zero address checks may be refactored as follows:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L14-L16

```
    constructor(address _manager) {
        require(_manager != address(0), "Zero address detected.");
        liquidStakingManager = ILiquidStakingManager(_manager);
    }
```
Additionally, consider also adding an optional codehash check for addresses at the constructor since the zero address check cannot guarantee a matching address has been inputted.

All other instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L14-L16
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L14-L16
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L19-L27

## Immutable Variables
State variables having no setter functions associated and can only be assigned at the constructor should be declared immutable.

As an example, the following code block needing immutable visibility may be refactored as follows: ,

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L12-L16

```
    ILiquidStakingManager public immutable liquidStakingManager;

    constructor(address _manager) {
        liquidStakingManager = ILiquidStakingManager(_manager);
    }
```
All other instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L13-L16
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L13-L16
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15-L22
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L15-L22
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L11-L27

## Descriptive Comments
Comments should be descriptive to convey its intended message rather than merely entailing abbreviations/symbols.

Here are the instances entailed:

[File: OwnableSmartWalletFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol)  

```
25:        emit WalletCreated(masterWallet, address(this)); // F: [OSWF-2]

29:        wallet = _createWallet(msg.sender); // F: [OSWF-1]

33:        wallet = _createWallet(owner); // F: [OSWF-1]

40:        IOwnableSmartWallet(wallet).initialize(owner); // F: [OSWF-1]

43:        emit WalletCreated(wallet, owner); // F: [OSWF-1]
```
## Lines Too Long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible. 

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L40
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L46

## Modifier for Identical Checks
Similar/identical require statement used in different functions of the same contract should be grouped into a modifier.

Here is a contract instance entailed:

[File: GiantLP.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol)

```
30:        require(msg.sender == pool, "Only pool");

35:        require(msg.sender == pool, "Only pool");
```
## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

There are a total of two instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L44-L45

```
        lastInteractedTimestamp[_from] = block.timestamp;
        lastInteractedTimestamp[_to] = block.timestamp;
```
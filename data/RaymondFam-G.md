## Use of Named Returns for Local Variables Saves Gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable. 

As an example, the following instance of code block can refactored as follows:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L11-L17

```
    function deploy(address _liquidStakingManager) external returns (OptionalHouseGatekeeper newKeeper) {
        newKeeper = new OptionalHouseGatekeeper(_liquidStakingManager);

        emit NewOptionalGatekeeperDeployed(address(newKeeper), _liquidStakingManager);
    }
```
All other function instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L19

```
    function isMemberPermitted(bytes calldata _blsPublicKeyOfKnot) external override view returns (bool) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol#L18

```
    function deploySavETHVault(address _liquidStakingManger, address _lpTokenFactory) external returns (address) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L18

```
    function deployStakingFundsVault(address _liquidStakingManager, address _tokenFactory) external returns (address) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L32

```
    ) external returns (address) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L56

```
    function liquidStakingManager() external view returns (address) {
```
[File: SyndicateFactory.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol)

```
26:    ) public override returns (address) {

52:    ) external override view returns (address) {

62:    ) public override pure returns (bytes32) {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L78

```
    ) public returns (address) {
```
[Line: OwnableSmartWallet.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol)

```
46:        returns (bytes memory)

61:        returns (bytes memory)

76:    returns (bytes memory)

88:        returns (address)

143:        returns (bool)
```
[Line: SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)

```
83:    function depositETHForStaking(bytes calldata _blsPublicKeyOfKnot, uint256 _amount) public payable returns (uint256) {

126:    function burnLPToken(LPToken _lpToken, uint256 _amount) public nonReentrant returns (uint256) {

203:    ) public onlyManager nonReentrant returns (uint256) {

218:    function isBLSPublicKeyPartOfLSDNetwork(bytes calldata _blsPublicKeyOfKnot) public virtual view returns (bool) {

223:    function isBLSPublicKeyBanned(bytes calldata _blsPublicKeyOfKnot) public view returns (bool) {

228:    function isDETHReadyForWithdrawal(address _lpTokenAddress) external view returns (bool) {
```
## Use Fixed-size `bytes32` instead of `string`
Fitting your data in fixed-size 32 byte words is much cheaper than using arbitrary-length types (string in this case). Remember that bytes32 uses less gas because it fits in a single EVM word. Typically, any fixed size variable in solidity is cheaper than dynamically sized ones. 

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L30-L31

```
        string calldata _tokenSymbol,
        string calldata _tokenName
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L77

```
        string calldata _stakehouseTicker
```
## Private/Internal Function Embedded Modifier Reduces Contract Size
Consider having the logic of a modifier embedded through a private (doesn't matter whether or not the contract entails any child contracts since the private visibility saves even more gas on function calls than the internal visibility) function to reduce contract size if need be. 

For instance, the following instance of modifier may be rewritten as follows:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L160-L163

```
    function _onlyDAO() private view {
        require(msg.sender == dao, "Must be DAO");
    }

    modifier onlyDAO() {
        _onlyDAO();
        _;
    }
```
All other modifier instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L22-L25
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L49-L52

## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.13",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:
```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Payable Access Control Functions Costs Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`. 

Here are the instances entailed:

[File: LPToken.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol)

```
46:    function mint(address _recipient, uint256 _amount) external onlyDeployer {

51:    function burn(address _recipient, uint256 _amount) external onlyDeployer {
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L114

```
    function setApproval(address to, bool status) external onlyOwner override {
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, consider replacing `>=` with the strict counterpart `>` in the following inequality instance:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L35

```
        require(msg.value > MIN_STAKING_AMOUNT - 1, "Minimum not supplied");
```
All other `>=` instances entailed:

[File: GiantPoolBase.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol)

```
53:        require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

54:        require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");

55:        require(idleETH >= _amount, "Come back later or withdraw less ETH");

94:        require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");

95:        require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");
```
[File: GiantSavETHVaultPool.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol)

```
92:                require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");

127:        require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
```
[Line: SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)

```
127:        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");

161:                require(dETHBalance >= 24 ether, "Nothing to withdraw");

204:        require(_amount >= 24 ether, "Amount cannot be less than 24 ether");

205:        require(address(this).balance >= _amount, "Insufficient withdrawal amount");
```
Similarly, as an example, consider replacing `<=` with the strict counterpart `<` in the following inequality instance:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L128

```
        require(_amount < _lpToken.balanceOf(msg.sender) + 1, "Not enough balance");
```
## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

As an example, the following `+=` instance entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L39

```
        idleETH = idleETH + msg.value;
```
All other `+=` instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L71

Similarly, as an example, the following `-=` instance entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L57

```
        idleETH = idleETH - _amount;
```
All other `-=` instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L46

## Unchecked SafeMath Saves Gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L76-L89

```
        for (uint256 i; i < amountOfTokens;) {
            LPToken token = _lpTokens[i];
            uint256 amount = _amounts[i];

            _assertUserHasEnoughGiantLPToClaimVaultLP(token, amount);

            // Burn giant LP from user before sending them an LP token from this pool
            lpTokenETH.burn(msg.sender, amount);

            // Giant LP tokens in this pool are 1:1 exchangeable with external savETH vault LP
            token.transfer(msg.sender, amount);

            emit LPSwappedForVaultLP(address(token), msg.sender, amount);

            unchecked {
                ++i;
            }
        }
```
All other for loop instances entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L42-L59
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L78-L102
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L146-L157
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L63-L73
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L103-L106
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L116-L118

## calldata and memory
When running a function we could pass the function parameters as calldata or memory for variables such as strings, bytes, structs, arrays etc. If we are not modifying the passed parameter, we should pass it as calldata because calldata is more gas efficient than memory.

Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. However, it alleviates the compiler from the `abi.decode()` step that copies each index of the calldata to the memory index, bypassing the cost of 60 gas on each iteration. 

Here are the instances entailed:

[File: OwnableSmartWallet.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol)

```
41:    function execute(address target, bytes memory callData)

54:        bytes memory callData,

69:        bytes memory callData,
```
## State Variables Repeatedly Read Should be Cached
SLOADs cost 100 gas each after the 1st one whereas MLOADs/MSTOREs only incur 3 gas each. As such, storage values read multiple times should be cached in the stack memory the first time (costing only 1 SLOAD) and then re-read from this cache to avoid multiple SLOADs.

`_isTransferApproved` could be cached and have the code block instance below refactored:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L131-L132

```
            bool isTransferApproved[from][to] =_isTransferApproved[from][to]; // SLOAD 1

131:        bool statusChanged = isTransferApproved[from][to] != status; // MLOAD 1
132:        isTransferApproved[from][to] = status; // F: [OSW-2], MLOAD 2
```
## Avoid Boolean Expressions Comparison to Boolean Literals in Conditional Checks
You will save deployment gas by not comparing boolean expressions to boolean literals in the `if` or `require` statements.

Here are the two instances entailed:

[File: SavETHVault.sol](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol)

```
64:            require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

84:        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");
```
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L149-L152

```
                require(
                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
                    "ETH is either staked or derivatives minted"
                );
```
## `abi.encode()` Costs More Gas Than `abi.encodePacked()`
Changing `abi.encode()` to `abi.encodePacked()` can save gas considering the former pads extra null bytes at the end of the call data, which is unnecessary. Please visit the following the link delineating how `abi.encodePacked()` is more gas efficient in general:

https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison

Here is one instance entailed:

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L63

```
        return keccak256(abi.encode(_deployer, _contractOwner, _numberOfInitialKnots));
```
### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Use `unchecked` in `withdrawETH` function | 1 |
| [G-02] | Functions guaranteed to revert_ when callled by normal users can be marked `payable`  | 17 |
| [G-03] | ```x -= y (x += y) ``` costs more gas than ```x = x – y (x = x + y)``` for state variables | 9 |
| [G-04] | Optimize names to save gas | All contracts |
| [G-05] | Use assembly to check for ```address(0)``` | 72|
| [G-06] | Use ``assembly`` to write _address storage values_| 10 |
| [G-07] | The solady Library's Ownable contract is significantly gas-optimized, which can be used  | 17 |
| [G-08] | Setting The Constructor To Payabl| 16|
| [G-09] | Use a different pattern when using for loops | 2 |
| [G-10] | Use inline assembly for contract balance | 8 |
| [G-11] | Use ``double require`` instead of using ``&&`` |9 |

Total 11 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Use `v4.8.0 OpenZeppelin` contracts |

Total 1 suggestion
### [G-01] Use `unchecked` in `withdrawETH` function

**Context:**

```solidity
contracts/liquid-staking/GiantPoolBase.sol:
  51      /// @param _amount of LP tokens user is burning in exchange for same amount of ETH
  52:     function withdrawETH(uint256 _amount) external nonReentrant {
  53:         require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
  54:         require(lpTokenETH.balanceOf(msg.sender) >= _amount, "Invalid balance");
  55:         require(idleETH >= _amount, "Come back later or withdraw less ETH");
  56: 
  57:         idleETH -= _amount;
  58: 
  59:         lpTokenETH.burn(msg.sender, _amount);
  60:         (bool success,) = msg.sender.call{value: _amount}("");
  61:         require(success, "Failed to transfer ETH");
  62: 
  63:         emit LPBurnedForETH(msg.sender, _amount);
  64:     }
```

**Description:**
In the `withdrawETH` function in the _GiantPoolBase.sol_ contract, the `idleETH` status variable will never be less than the `_amount` function parameter |(require(idleETH >= _amount, "Come back later or withdraw less ETH");)| underflow does not occur. So here unchecked can be used.

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {

    Contract0 c0;
    Contract1 c1;


    function setUp() public {

        c0 = new Contract0();
        c1 = new Contract1();
    }

        function testGas() public {
            c0.withdrawETH(5);
            c1.withdrawETH(5);            
        }
}

contract Contract0 {
    uint256 idleETH = 10;

    function withdrawETH(uint256 _amount) external {
        require(idleETH >= _amount, "Come back later or withdraw less ETH");
        idleETH -= _amount;    
    }
}


contract Contract1 {
    uint256 idleETH = 10;

    function withdrawETH(uint256 _amount) external {
        require(idleETH >= _amount, "Come back later or withdraw less ETH");
        unchecked {
         idleETH -= _amount;
        }
    }
}
```

**Gas Report:**
```js
╭──────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/test.sol:Contract0 contract ┆                 ┆      ┆        ┆      ┆         │
╞══════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 99029                                ┆ 421             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ withdrawETH                          ┆ 5419            ┆ 5419 ┆ 5419   ┆ 5419 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
╭──────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/test.sol:Contract1 contract ┆                 ┆      ┆        ┆      ┆         │
╞══════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 82217                                ┆ 337             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ withdrawETH                          ┆ 5346            ┆ 5346 ┆ 5346   ┆ 5346 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
```

### [G-02]  Functions guaranteed to revert_ when callled by normal users can be marked `payable` 

**Context:** 

```solidity
17 results - 5 files

contracts/smart-wallet/OwnableSmartWallet.sol:
  114:     function setApproval(address to, bool status) external onlyOwner override {

contracts/syndicate/Syndicate.sol:
  145      function registerKnotsToSyndicate(
  146          bytes[] calldata _newBLSPublicKeyBeingRegistered
  147:     ) external onlyOwner {
  154:     function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {
  161:     function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {
  168:     function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {

contracts/liquid-staking/LiquidStakingManager.sol:
  218:     function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {
  226      function restoreFreeFloatingSharesToSmartWalletForRageQuit(
  227         address _smartWallet,
  228         bytes[] calldata _blsPublicKeys,
  229         uint256[] calldata _amounts
  230:     ) external onlyDAO {
  239:     function updateDAOAddress(address _newAddress) external onlyDAO {
  249:     function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {
  255:     function updateTicker(string calldata _newTicker) external onlyDAO {
  267:     function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {
  278:     function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {
  308:     function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO {
 
contracts/liquid-staking/SavETHVault.sol:
  200     function withdrawETHForStaking(
  201          address _smartWallet,
  202          uint256 _amount
  203:     ) public onlyManager nonReentrant returns (uint256) {

contracts/liquid-staking/StakingFundsVault.sol:
   56:     function updateDerivativesMinted() external onlyManager {
  239:     function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
  255      function unstakeSyndicateSharesForRageQuit(
  256          address _sETHRecipient,
  257          bytes[] calldata _blsPublicKeys,
  258          uint256[] calldata _amounts
  259:     ) external onlyManager nonReentrant {
```

**Description:**
If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only ```onlyOwner or onlyDAO or onlyManager``` functions)

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```diff
- function registerKnotsToSyndicate(
        bytes[] calldata _newBLSPublicKeyBeingRegistered
    ) external onlyOwner {
        // update accrued ETH per SLOT type
        updateAccruedETHPerShares();
        _registerKnotsToSyndicate(_newBLSPublicKeyBeingRegistered);
    }

+ function registerKnotsToSyndicate(
        bytes[] calldata _newBLSPublicKeyBeingRegistered
    ) external payable onlyOwner {
        // update accrued ETH per SLOT type
        updateAccruedETHPerShares();
        _registerKnotsToSyndicate(_newBLSPublicKeyBeingRegistered);
    }
```

**Gas Report:**
```js
╭──────────────────────────────────────────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
│ contracts/testing/syndicate/SyndicateMock.sol:SyndicateMock contract ┆                 ┆        ┆        ┆        ┆         │
╞══════════════════════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
│ Deployment Cost                                                      ┆ Deployment Size ┆        ┆        ┆        ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 2990771                                                              ┆ 15093           ┆        ┆        ┆        ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                                                        ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ registerKnotsToSyndicate                                             ┆ 6503            ┆ 57756  ┆ 64257  ┆ 108857 ┆ 5       │
╰──────────────────────────────────────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
╭──────────────────────────────────────────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
│ contracts/testing/syndicate/SyndicateMock.sol:SyndicateMock contract ┆                 ┆        ┆        ┆        ┆         │
╞══════════════════════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
│ Deployment Cost                                                      ┆ Deployment Size ┆        ┆        ┆        ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 2980355                                                              ┆ 15041           ┆        ┆        ┆        ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                                                        ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ registerKnotsToSyndicate                                             ┆ 6479            ┆ 57732  ┆ 64233  ┆ 108833 ┆ 5       │
╰──────────────────────────────────────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
```

### [G-03] `x -= y (x += y)` costs more gas than `x = x – y (x = x + y)` for state variables

**Description:**
`x -= y` costs more gas than `x = x – y` for state variables.

**Context:**
```solidity
9 results - 5 files

contracts/liquid-staking/GiantMevAndFeesPool.sol:
  40:             idleETH -= _ETHTransactionAmounts[i];

contracts/liquid-staking/GiantPoolBase.sol:
  57:             idleETH -= _amount;    
         
contracts/liquid-staking/GiantSavETHVaultPool.sol:
  46:             idleETH -= transactionAmount;

contracts/liquid-staking/LiquidStakingManager.sol:
  615:             stakedKnotsOfSmartWallet[smartWallet] -= 1;

contracts/syndicate/Syndicate.sol:
  269:             totalFreeFloatingShares -= _sETHAmount;
  272:             sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
  273:             sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;
  621:             totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];
  624:             numberOfRegisteredKnots -= 1;

contracts/liquid-staking/StakingFundsVault.sol:
   58:         totalShares += 4 ether;
   96          uint256 amount = _amounts[i];
   97:         totalAmount += amount;
  277          LPToken token = lpTokenForKnot[_blsPubKeys[i]];
  278:         totalAccumulated += previewAccumulatedETH(_user, token);
  287:         totalAccumulated += previewAccumulatedETH(_user, _token[i]);
 
contracts/liquid-staking/SyndicateRewardsProcessor.sol:
  65:           totalClaimed += due;
  85:           accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;
 
contracts/syndicate/Syndicate.sol:
  185:          accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);

  190:          accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
  225:          totalFreeFloatingShares += _sETHAmount;
  226:          sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
  227:          sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
  317:          totalClaimed += unclaimedUserShare;
  511:          accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;
  558:          numberOfRegisteredKnots += knotsToRegister;
  658:          totalClaimed += unclaimedUserShare;
```

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
  
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        c0.swap(2,3);
        c1.swap1(2,3);
    }
}
contract Contract0 {
    uint256 public amountIn = 10;
    
    function swap(uint256 amountInToBin, uint256 fee)  external returns (uint256 ) {
       return amountIn -= amountInToBin + fee;
    }
}

contract Contract1 {
    uint256 public amountIn = 10;
    
    function swap1(uint256 amountInToBin, uint256 fee)  external returns (uint256 result1) {
       return (amountIn = amountIn - (amountInToBin + fee));
    }
}
```
**Gas Report:**
```js
╭──────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/test.sol:Contract0 contract ┆                 ┆      ┆        ┆      ┆         │
╞══════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 83017                                ┆ 341             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ swap                                 ┆ 5454            ┆ 5454 ┆ 5454   ┆ 5454 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
╭──────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/test.sol:Contract1 contract ┆                 ┆      ┆        ┆      ┆         │
╞══════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 83017                                ┆ 341             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ swap1                                ┆ 5431            ┆ 5431 ┆ 5431   ┆ 5431 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
```

### [G-04] Optimize names to save gas

**Context:** 
All Contracts

**Description:** 
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` Syndicate.sol``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

Syndicate.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
793193ae  =>  initialize(address,uint256,address[],bytes[])
d383eb94  =>  registerKnotsToSyndicate(bytes[])
42e5ddee  =>  deRegisterKnots(bytes[])
964bb65e  =>  addPriorityStakers(address[])
e09e6d7f  =>  updatePriorityStakingBlock(uint256)
6d4ef6b9  =>  updateAccruedETHPerShares()
8462c5c8  =>  stake(bytes[],uint256[],address)
cda82f20  =>  unstake(address,address,bytes[],uint256[])
7f3a7f7f  =>  claimAsStaker(address,bytes[])
30237f5e  =>  claimAsCollateralizedSLOTOwner(address,bytes[])
12499ee9  =>  updateCollateralizedSlotOwnersAccruedETH(bytes)
dbd13782  =>  batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[])
758f2941  =>  calculateUnclaimedFreeFloatingETHShare(bytes,address)
4a65fbb5  =>  calculateETHForFreeFloatingOrCollateralizedHolders()
4c480979  =>  previewUnclaimedETHAsFreeFloatingStaker(address,bytes)
5271337a  =>  previewUnclaimedETHAsCollateralizedSlotOwner(address,bytes)
1f377fed  =>  getUnprocessedETHForAllFreeFloatingSlot()
b909757f  =>  getUnprocessedETHForAllCollateralizedSlot()
0bba3f7b  =>  calculateNewAccumulatedETHPerFreeFloatingShare()
94058b1c  =>  calculateNewAccumulatedETHPerCollateralizedSharePerKnot()
6e1682a0  =>  totalETHReceived()
ed3e91d6  =>  _initialize(address,uint256,address[],bytes[])
17430f08  =>  _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes)
ba345cbb  =>  _calculateCollateralizedETHOwedPerKnot()
5dd47bdd  =>  _calculateNewAccumulatedETHPerCollateralizedShare(uint256)
2896c45f  =>  _calculateNewAccumulatedETHPerFreeFloatingShare(uint256)
f7919ba4  =>  _registerKnotsToSyndicate(bytes[])
701d5499  =>  _addPriorityStakers(address[])
5f18ea80  =>  _deRegisterKnots(bytes[])
ab11a8e3  =>  _deRegisterKnot(bytes)
35acddeb  =>  _getCorrectAccumulatedETHPerFreeFloatingShareForBLSPublicKey(bytes)
bb7c015b  =>  _claimAsStaker(address,bytes[])
```

### [G-05] Use assembly to check for ```address(0)```

**Context:** 
```solidity
72 results - 13 files

contracts/liquid-staking/ETHPoolLPFactory.sol:
   77:         require(address(_oldLPToken) != address(0), "Zero address");
   78:         require(address(_newLPToken) != address(0), "Zero address");
  117:         if(address(lpToken) != address(0)) {

contracts/liquid-staking/GiantLP.sol:
  40:         if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).beforeTokenTransfer(_from, _to, _amount);
  46:         if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).afterTokenTransfer(_from, _to, _amount);
 
contracts/liquid-staking/GiantMevAndFeesPool.sol:
  151:         if (_from != address(0)) {
  
contracts/liquid-staking/LiquidStakingManager.sol:
  209:         require(smartWallet != address(0), "No wallet found");
  240:         require(_newAddress != address(0), "Zero address");
  279:         require(_nodeRunner != address(0), "Zero address");
  290:         require(_newRepresentative != address(0), "Zero address");
  294:         require(smartWallet != address(0), "No smart wallet");
  309:         require(_newRepresentative != address(0), "Zero address");
  312:         require(smartWallet != address(0), "No smart wallet");
  327:         require(_recipient != address(0), "Zero address");
  357:         require(_new != address(0) && _current != _new, "New is zero or current");
  360:         require(wallet != address(0), "Wallet does not exist");
  364:         require(newRunnerCurrentWallet == address(0), "New runner has a wallet");
  387:         require(_recipient != address(0), "Zero address");
  390:         require(smartWallet != address(0), "Unknown node runner");
  441:         if(smartWallet == address(0)) {
  454:         if(smartWalletRepresentative[smartWallet] != address(0)) {
  496:         return smartWalletOfKnot[_blsPublicKeyOfKnot] != address(0);
  501:         return !isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot) || bannedBLSPublicKeys[_blsPublicKeyOfKnot] != address(0);
  544:         require(associatedSmartWallet != address(0), "Unknown BLS public key");
  563:         if(representative != address(0)) {
  658:         require(_dao != address(0), "Zero address");
  659:         require(_syndicateFactory != address(0), "Zero address");
  660:         require(_smartWalletFactory != address(0), "Zero address");
  661:         require(_brand != address(0), "Zero address");
  685:         require(_nodeRunner != address(0), "Zero address");
  700:         if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) {
  717:         else if(_isEnabled && smartWalletRepresentative[_smartWallet] == address(0)) {
  854:         if (address(gatekeeper) != address(0)) {
  939:         require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
  943:         require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");
 

contracts/liquid-staking/LPToken.sol:
  62:         if (address(transferHookProcessor) != address(0)) transferHookProcessor.beforeTokenTransfer(_from, _to, _amount);
  69:         if (address(transferHookProcessor) != address(0)) transferHookProcessor.afterTokenTransfer(_from, _to, _amount);
 
contracts/liquid-staking/LPTokenFactory.sol:
  19:         require(_lpTokenImplementation != address(0), "Address cannot be zero");
  33:         require(address(_deployer) != address(0), "Zero address");

contracts/liquid-staking/LSDNFactory.sol:
  51:         require(_liquidStakingManagerImplementation != address(0), "Zero Address");
  52:         require(_syndicateFactory != address(0), "Zero Address");
  53:         require(_lpTokenFactory != address(0), "Zero Address");
  54:         require(_smartWalletFactory != address(0), "Zero Address");
  55:         require(_brand != address(0), "Zero Address");
  56:         require(_savETHVaultDeployer != address(0), "Zero Address");
  57:         require(_stakingFundsVaultDeployer != address(0), "Zero Address");
  58:         require(_optionalGatekeeperDeployer != address(0), "Zero Address");

contracts/liquid-staking/SavETHVault.sol:
  206:         require(_smartWallet != address(0), "Zero address");
  236:         require(_liquidStakingManagerAddress != address(0), "Zero address");
  237:         require(address(_lpTokenFactory) != address(0), "Zero address");

contracts/liquid-staking/StakingFundsVault.sol:
   86:         if (address(tokenForKnot) != address(0)) {
  127:         if (address(tokenForKnot) != address(0)) {
  154:         require(address(token) != address(0), "No ETH staked for specified BLS key");
  177:         require(address(_lpToken) != address(0), "Zero address specified");
  229:         require(address(token) != address(0), "Invalid BLS key");
  242:         require(_wallet != address(0), "Zero address");
  317:         if (syndicate != address(0)) {
  332:         if (_from != address(0)) {
  344:         if (LiquidStakingManager(payable(liquidStakingNetworkManager)).syndicate() != address(0)) {
  361:         require(_syndicate != address(0), "Invalid configuration");
  372:         require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");
  373:         require(address(_lpTokenFactory) != address(0), "Zero Address");
  
contracts/liquid-staking/SyndicateRewardsProcessor.sol:
  57:         require(_recipient != address(0), "Zero address");

contracts/smart-wallet/OwnableSmartWallet.sol:
   34:             initialOwner != address(0),
  116:             to != address(0),

contracts/smart-wallet/OwnableSmartWalletFactory.sol:
  37:         require(owner != address(0), 'Wallet cannot be address 0');

contracts/syndicate/Syndicate.sol:
  206:         if (_onBehalfOf == address(0)) revert ZeroAddress();
  231:         if (stakeHouse == address(0)) revert KnotIsNotAssociatedWithAStakeHouse();
  253:         if (_unclaimedETHRecipient == address(0)) revert ZeroAddress();
  254:         if (_sETHRecipient == address(0)) revert ZeroAddress();
  295:         if (_recipient == address(0)) revert ZeroAddress();
  642:         if (_recipient == address(0)) revert ZeroAddress();
```

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    function testGas() public view {
        c0.setOperator(address(this));
        c1.setOperator(address(this));
    }
}
contract Contract0 {
    function setOperator(address operator_) public pure {
        require(operator_) != address(0), "INVALID_RECIPIENT");    }
}
contract Contract1 {
    function setOperator(address operator_) public pure {
        assembly {
            if iszero(operator_) {
                mstore(0x00, "Callback_InvalidParams")
                revert(0x00, 0x20)
            }
        }
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 50899                                     ┆ 285             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setOperator                               ┆ 258             ┆ 258 ┆ 258    ┆ 258 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 44893                                     ┆ 255             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setOperator                               ┆ 252             ┆ 252 ┆ 252    ┆ 252 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
```

### [G-06] Use ``assembly`` to write _address storage values_ 

**Context:**
[GiantLP.sol#L25](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol#L25), [GiantMevAndFeesPool.sol#L19](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L19), [GiantSavETHVaultPool.sol#L20](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol#L20), [LiquidStakingManager.sol#L245](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L245), [LPToken.sol#L38](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPToken.sol#L38), [LPTokenFactory.sol#L21](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol#L21), [LSDNFactory.sol#L60-L67](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol#L60-L67), [SavETHVault.sol#L239](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVault.sol#L239), [StakingFundsVault.sol#L375-L376](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol#L375-L376), [SyndicateFactory.sol#L17](https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol#L17)

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTestFoundry is DSTest {
    Contract1 c1;
    Contract2 c2;
    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }
    
    function testGas() public {
        c1.setRewardTokenAndAmount(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4,356);
        c2.setRewardTokenAndAmount(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4,356);
    }
}

contract Contract1  {
    address rewardToken ;
    uint256 reward;

    function setRewardTokenAndAmount(address token_, uint256 reward_) external {
        rewardToken = token_;
        reward = reward_;
    }
}

contract Contract2  {
    address rewardToken ;
    uint256 reward;

    function setRewardTokenAndAmount(address token_, uint256 reward_) external {
        assembly {
            sstore(rewardToken.slot, token_)
            sstore(reward.slot, reward_)           

        }
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 50899                                     ┆ 285             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setRewardTokenAndAmount                   ┆ 44490           ┆ 44490 ┆ 44490  ┆ 44490 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract2 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 38087                                     ┆ 221             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setRewardTokenAndAmount                   ┆ 44457           ┆ 44457 ┆ 44457  ┆ 44457 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```

### [G-07] The solady Library's Ownable contract is significantly gas-optimized, which can be used

**Description:**
The project uses the `onlyOwner, onlyDAO, onlyManager` authorization model I recommend using _Solady's highly gas optimized contract._

https://github.com/Vectorized/solady/blob/main/src/auth/OwnableRoles.sol

```solidity
17 results - 5 files

contracts/smart-wallet/OwnableSmartWallet.sol:
  114:     function setApproval(address to, bool status) external onlyOwner override {

contracts/syndicate/Syndicate.sol:
  145      function registerKnotsToSyndicate(
  146          bytes[] calldata _newBLSPublicKeyBeingRegistered
  147:     ) external onlyOwner {
  154:     function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {
  161:     function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {
  168:     function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {

contracts/liquid-staking/LiquidStakingManager.sol:
  218:     function deRegisterKnotFromSyndicate(bytes[] calldata _blsPublicKeys) external onlyDAO {
  226      function restoreFreeFloatingSharesToSmartWalletForRageQuit(
  227         address _smartWallet,
  228         bytes[] calldata _blsPublicKeys,
  229          uint256[] calldata _amounts
  230:     ) external onlyDAO {
  239:     function updateDAOAddress(address _newAddress) external onlyDAO {
  249:     function updateDAORevenueCommission(uint256 _commissionPercentage) external onlyDAO {
  255:     function updateTicker(string calldata _newTicker) external onlyDAO {
  267:     function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {
  278:     function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {
  308:     function rotateEOARepresentativeOfNodeRunner(address _nodeRunner, address _newRepresentative) external onlyDAO {
 
contracts/liquid-staking/SavETHVault.sol:
  200     function withdrawETHForStaking(
  201          address _smartWallet,
  202          uint256 _amount
  203:     ) public onlyManager nonReentrant returns (uint256) {

contracts/liquid-staking/StakingFundsVault.sol:
   56:     function updateDerivativesMinted() external onlyManager {
  239:     function withdrawETH(address _wallet, uint256 _amount) public onlyManager nonReentrant returns (uint256) {
  255      function unstakeSyndicateSharesForRageQuit(
  256          address _sETHRecipient,
  257          bytes[] calldata _blsPublicKeys,
  258          uint256[] calldata _amounts
  259:     ) external onlyManager nonReentrant {
```

### [G-08] Setting The Constructor To Payable

**Description:**
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

**Context:**
```solidity
16 results - 16 files

contracts/liquid-staking/GiantLP.sol:
  19:     constructor(
  20      address _pool,
  21      address _transferHookProcessor,
  22      string memory _name,
  23      string memory _symbol
  24      ERC20(_name, _symbol) {

contracts/liquid-staking/GiantMevAndFeesPool.sol:
  17:     constructor(LSDNFactory _factory) {
 
contracts/liquid-staking/GiantSavETHVaultPool.sol:
  18:     constructor(LSDNFactory _factory) {
  
contracts/liquid-staking/LiquidStakingManager.sol:
  166:     constructor() initializer {}

contracts/liquid-staking/LPToken.sol:
  28:     constructor() initializer {} 

contracts/liquid-staking/LPTokenFactory.sol:
  18:     constructor(address _lpTokenImplementation) {

contracts/liquid-staking/LSDNFactory.sol:
  41:     constructor(
  42          address _liquidStakingManagerImplementation,
  43          address _syndicateFactory,
  44          address _lpTokenFactory,
  45          address _smartWalletFactory,
  46          address _brand,
  47          address _savETHVaultDeployer,
  48          address _stakingFundsVaultDeployer,
  49          address _optionalGatekeeperDeployer
  50       ) {

contracts/liquid-staking/OptionalHouseGatekeeper.sol:
  14:     constructor(address _manager) {
  15          liquidStakingManager = ILiquidStakingManager(_manager);

contracts/liquid-staking/SavETHVault.sol:
  43:     constructor() initializer {}
  
contracts/liquid-staking/SavETHVaultDeployer.sol:
  14:     constructor() {

contracts/liquid-staking/StakingFundsVault.sol:
  43:     constructor() initializer {}
 
contracts/liquid-staking/StakingFundsVaultDeployer.sol:
  14:     constructor() {

contracts/smart-wallet/OwnableSmartWallet.sol:
  25:     constructor() initializer {}

contracts/smart-wallet/OwnableSmartWalletFactory.sol:
  22:     constructor() {

contracts/syndicate/Syndicate.sol:
  123:     constructor() initializer {}

contracts/syndicate/SyndicateFactory.sol:
  16:     constructor(address _syndicateImpl) {
```

**Recommendation:**
Set the constructor to ```payable```

**Proof Of Concept:**
https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5?u=pcaversaccio

The optimizer was turned on and set to 10000 runs

```js
contract GasTestFoundry is DSTest {
    
    Contract1 c1;
    Contract2 c2;
    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }
    
    function testGas() public {
        c1.x();
        c2.x();
    }
}

contract Contract1 {
    
    uint256 public dummy;
    constructor() payable {
        dummy = 1;
    }
    
    function x() public {
    }
}

contract Contract2 {
    
    uint256 public dummy;
    constructor() {
        dummy = 1;
    }
    
    function x() public {
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49563                                     ┆ 159             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤

╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract2 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49587                                     ┆ 172             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
```

### [G-09] Use a different pattern when using for loops

**Context:**
```js
2 results - 1 file

contracts/liquid-staking/LiquidStakingManager.sol:
  391  
  392:         for(uint256 i; i < _blsPubKeys.length; ++i) {
  393              require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

  464  
  465:         for(uint256 i; i < len; ++i) {
  466              bytes calldata _blsPublicKey = _blsPublicKeys[i];
```

**Description:**
In the use of for loops, this structure, which will reduce gas costs, can be preferred. It saves approximately 400 gas in an array with 6 members.

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs
```js
contract GasTest is DSTest {
  
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        address[] memory Inputs = new address[](6);
        Inputs[0] = 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4;
        Inputs[1] = 0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2;
        Inputs[2] = 0x4B20993Bc481177ec7E8f571ceCaE8A9e22C02db;
        Inputs[3] = 0x78731D3Ca6b7E34aC0F824c42a7cC18A495cabaB;
        Inputs[4] = 0x617F2E2fD72FD9D5503197092aC168c91465E7f2;
        Inputs[5] = 0x17F6AD8Ef982297579C203069C1DbfFE4348c372;
        
        c0.dummy(Inputs);
        c1.dummy(Inputs);
    }
}

contract Contract0 {
    function dummy(address[] memory test) public {
        for (uint256 i = 0; i < test.length; ++i) {
        }
    }
}

contract Contract1 { 
    function dummy(address[] memory test) public {
        for (uint256 i = 0; i < test.length; i = unchecked_inc(i)) {
            // do something that doesn't change the value of i
        }
    }
    
    function unchecked_inc(uint i) internal pure returns (uint) {
        unchecked {
            return i + 1;
        }
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆      ┆        ┆      ┆         │
╞═══════════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 113159                                    ┆ 597             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ dummy                                     ┆ 2044            ┆ 2044 ┆ 2044   ┆ 2044 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆      ┆        ┆      ┆         │
╞═══════════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 92541                                     ┆ 494             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ dummy                                     ┆ 1666            ┆ 1666 ┆ 1666   ┆ 1666 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
```
### [G-10]  Use inline assembly for contract balance

**Context:**
```js
8 results - 7 files

contracts/liquid-staking/GiantMevAndFeesPool.sol:
  177:         return address(this).balance + totalClaimed - idleETH;

contracts/liquid-staking/LiquidStakingManager.sol:
  400:         uint256 balBefore = address(this).balance;
  410:         (uint256 nodeRunnerAmount, uint256 daoAmount) = _calculateCommission(address(this).balance - balBefore);

contracts/liquid-staking/SavETHVault.sol:
  205:         require(address(this).balance >= _amount, "Insufficient withdrawal amount");
 
contracts/liquid-staking/StakingFundsVault.sol:
  241:         require(_amount <= address(this).balance, "Not enough ETH to withdraw");

contracts/liquid-staking/SyndicateRewardsProcessor.sol:
  94:         return address(this).balance + totalClaimed;

contracts/syndicate/Syndicate.sol:
  465:         return address(this).balance + totalClaimed;

contracts/testing/liquid-staking/MockSavETHVault.sol:
  44:         return address(this).balance;
```

**Description:**
You  can use ```balance(address)``` instead of ```address.balance()``` when getting an contract’s balance of ETH.

**Proof Of Concept**
The optimizer was turned on and set to 10000 runs

```js
contract GasTest is DSTest {
    
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        
        c0._withdraw(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4);
        c1._withdraw(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4);
    }
}

contract Contract0 {
    
    function _withdraw(address addr) external {
        uint256 amount = address(this).balance;
        (bool sent, ) = addr.call{ value: amount }('');
    }
}

contract Contract1 {
    
    function _withdraw(address addr) external returns (uint256) {
        uint256 amount ;
        assembly {
            amount := selfbalance()
        }
        (bool sent, ) = addr.call{ value: amount }('');
    }
}

```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆      ┆        ┆      ┆         │
╞═══════════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 55305                                     ┆ 308             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ _withdraw                                 ┆ 2949            ┆ 2949 ┆ 2949   ┆ 2949 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 59511                                     ┆ 329             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ _withdraw                                 ┆ 509             ┆ 509 ┆ 509    ┆ 509 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
```
### [G-11] Use ``double require`` instead of using ``&&``

**Context:** 
```solidity
9 results - 3 files

contracts/liquid-staking/LiquidStakingManager.sol:
  357:         require(_new != address(0) && _current != _new, "New is zero or current");
  371:         if (msg.sender == dao && _wasPreviousNodeRunnerMalicious) {
  700:         if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) {
  717:         else if(_isEnabled && smartWalletRepresentative[_smartWallet] == address(0)) {

contracts/liquid-staking/StakingFundsVault.sol:
  215:         if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {
 
contracts/syndicate/Syndicate.sol:
  218:         if (block.number < priorityStakingEndBlock && !isPriorityStaker[_onBehalfOf]) revert NotPriorityStaker();
  500:         if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) { 
  533:         if (!isActive && !isNoLongerPartOfSyndicate[_blsPubKey]) {
  588:         if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();
```
**Description:**
Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

**Recommendation:**
LiquidStakingManager.sol:#L357;
 ```js
require(_new != address(0) && _current != _new, "New is zero or current");
```

Recommendation Code:
```js
require(_new != address(0), "New is zero"); 
require(_current != _new, "New is current"); 
```

### [S-01] Use `v4.8.0 OpenZeppelin` contracts

**Description:**
The upcoming v4.8.0 version of OpenZeppelin provides many small gas optimizations.

https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.8.0-rc.0

```js
v4.8.0-rc.0
⛽ Many small optimizations
```
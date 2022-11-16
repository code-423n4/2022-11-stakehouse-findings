
##  [1] EVENT IS MISSING INDEXED FIELDS

### Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

> There are   instances of this issue:


         FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

         16:   event ETHWithdrawnByDepositor(address depositor, uint256 amount);
        
         19:   event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

         22:   event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

         25:   event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);


###

##  [2] public MODIFIER CAN BE CHANGED TO internal in **rotateLPTokens()** .  rotateLPTokens() IS USED WITHIN THE FUNCTIONS. IF WE DECLARE  rotateLPTokens() AS A public  CAN BE ACCESSIBLE FROM OUTSIDE OF THE SMART CONTRACTS.

              FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol
 
              76:  function rotateLPTokens(LPToken _oldLPToken, LPToken _newLPToken, uint256 _amount) public {

##              

##  [3]   USUAL SUSPECTS: LACK OF ZERO CHECKS FOR NEW ADDRESSES

###  Constructors don’t have zero-checks, which could force a re-deployment, funds are not at risk in those cases.


        FILE: 2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol 

       constructor(
        address _pool,
        address _transferHookProcessor,
        string memory _name,
        string memory _symbol
        ) ERC20(_name, _symbol) {
        pool = _pool;
        transferHookProcessor = ITransferHookProcessor(_transferHookProcessor);
       }

##

## [4] REQUIRE STATEMENTS THROUGHS WRONG OR MEANING LESS  ERROR COMMANDS 

> There are   instances of this issue:

      2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol

      30:    require(msg.sender == pool, "Only pool");

      35:    require(msg.sender == pool, "Only pool");

##

##   [5] CONSTANT REDEFINED ELSEWHERE

###    Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.

##  

## [6]  IN BEST CODE PRACTICE FIRST NEED TO CHECK CONDITIONS THEN CAN ASSIGN VALUES FOR VARIABLES.

###   In batchDepositETHForStaking() the value of the numOfVaults is assigned before checking the conditions. This not a best code practice. If the first require statement failed then no use for numOfVaults  assignment. 

> There are   instances of this issue:

        2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

       function batchDepositETHForStaking(
        address[] calldata _stakingFundsVault,
        uint256[] calldata _ETHTransactionAmounts,
        bytes[][] calldata _blsPublicKeyOfKnots,
        uint256[][] calldata _amounts
         ) external {
         uint256 numOfVaults = _stakingFundsVault.length;   //@audit 
        require(numOfVaults > 0, "Zero vaults");
        require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");
        require(numOfVaults == _amounts.length, "Inconsistent lengths");

Proof Of Concept : 
  
###  The numOfVaults  should be assigned after all require conditions true. 

        function batchDepositETHForStaking(
        address[] calldata _stakingFundsVault,
        uint256[] calldata _ETHTransactionAmounts,
        bytes[][] calldata _blsPublicKeyOfKnots,
        uint256[][] calldata _amounts
        ) external {
       require(numOfVaults > 0, "Zero vaults");
        require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");
        require(numOfVaults == _amounts.length, "Inconsistent lengths");
        uint256 numOfVaults = _stakingFundsVault.length;    // AUDIT numOfRotations 


### Same problem in  function batchRotateLPTokens() 

       uint256 numOfRotations = _stakingFundsVaults.length;  // AUDIT numOfRotations 
        require(numOfRotations > 0, "Empty arrays");
        require(numOfRotations == _oldLPTokens.length, "Inconsistent arrays");
        require(numOfRotations == _newLPTokens.length, "Inconsistent arrays");
        require(numOfRotations == _amounts.length, "Inconsistent arrays");
        require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");
        for (uint256 i; i < numOfRotations; ++i) {
            StakingFundsVault(payable(_stakingFundsVaults[i])).batchRotateLPTokens(_oldLPTokens[i], _newLPTokens[i], _amounts[i]);
        }

###  2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol

        function withdrawLPTokens(LPToken[] calldata _lpTokens, uint256[] calldata _amounts) external {
        uint256 amountOfTokens = _lpTokens.length;     // @AUDIT amountOfTokens 
        require(amountOfTokens > 0, "Empty arrays");
        require(amountOfTokens == _amounts.length, "Inconsistent array lengths");

##

##  [7]   It is bad practice to use numbers directly in code without explanation . Here 0.5 Ethers directly check with lpTokenETH.balanceOf(msg.sender) .  No explanation about 0.5 ether check 

          2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

          116:    require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

## 

## [8]  Solidity pragma versioning should be upgraded to latest available version. Currently the solidity version in contracts is ^0.8.13 which was found to possess some bugs.  The latest solidity version is 0.8.17 

##

## [9]   INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

> There are   instances of this issue:

          2022-11-stakehouse/contracts/interfaces/IBrandNFT.sol

          pragma solidity ^0.8.13;

         2022-11-stakehouse/contracts/interfaces/ILPTokenInit.sol

         pragma solidity ^0.8.13;

         2022-11-stakehouse/contracts/interfaces/ISyndicateFactory.sol

         pragma solidity ^0.8.13;

         2022-11-stakehouse/contracts/interfaces/ISyndicateInit.sol

         pragma solidity ^0.8.13;

         2022-11-stakehouse/contracts/interfaces/ITransferHookProcessor.sol

         pragma solidity ^0.8.13;

##

## [10]  SPDX-License-Identifier: STATEMENT SHOULD BE DECLARED BEFORE pragma solidity ^0.8.13; DECLARATIONS . IN MANY INTERFACES LICENSE STATEMENT ADDED BEFORE PRAGMA STATEMENT . THE BEST SODE PRACTICE IS license DECLARATIONS BEFORE pragma 

> There are   instances of this issue:

          2022-11-stakehouse/contracts/interfaces/IGateKeeper.sol

           pragma solidity 0.8.13;

          // SPDX-License-Identifier: MIT

          2022-11-stakehouse/contracts/interfaces/ILPTokenInit.sol
 
           pragma solidity ^0.8.13;

           // SPDX-License-Identifier: MIT

           2022-11-stakehouse/contracts/interfaces/ILiquidStakingManager.sol

           pragma solidity 0.8.13;

           // SPDX-License-Identifier: MIT

           2022-11-stakehouse/contracts/interfaces/ILiquidStakingManagerChildContract.sol

           pragma solidity ^0.8.13;

           // SPDX-License-Identifier: MIT

           2022-11-stakehouse/contracts/interfaces/ISyndicateFactory.sol

           pragma solidity ^0.8.13;

          // SPDX-License-Identifier: MIT

           2022-11-stakehouse/contracts/interfaces/ISyndicateInit.sol

           pragma solidity ^0.8.13;

          // SPDX-License-Identifier: MIT

          2022-11-stakehouse/contracts/interfaces/ITransferHookProcessor.sol
       
          pragma solidity ^0.8.13;

         // SPDX-License-Identifier: MIT
      
##

##  [11]  
       




         
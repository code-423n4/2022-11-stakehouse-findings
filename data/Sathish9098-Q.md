
##  [1] EVENT IS MISSING INDEXED FIELDS

### Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

> There are 10 instances of this issue:

> FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

         16:   event ETHWithdrawnByDepositor(address depositor, uint256 amount);
        
         19:   event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

         22:   event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

         25:   event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

> FILE:  2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol 

         22:     event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

         19:      event DETHRedeemed(address depositor, uint256 amount);

> FILE:  2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol

        25:     event ETHDeposited(address sender, uint256 amount);

       28:      event ETHWithdrawn(address receiver, address admin, uint256 amount);

      31:       event ERC20Recovered(address admin, address recipient, uint256 amount);

      34:       event WETHUnwrapped(address admin, uint256 amount);

      


###

##  [2]  public MODIFIER CAN BE CHANGED TO internal in **rotateLPTokens()** .  rotateLPTokens() IS USED WITHIN THE FUNCTIONS. IF WE DECLARE  rotateLPTokens() AS A public  CAN BE ACCESSIBLE FROM OUTSIDE OF THE SMART CONTRACTS.

>FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol
 
              76:  function rotateLPTokens(LPToken _oldLPToken, LPToken _newLPToken, uint256 _amount) public {

##              

##  [3]   USUAL SUSPECTS: LACK OF ZERO CHECKS FOR NEW ADDRESSES

###  Constructors don’t have zero-checks, which could force a re-deployment, funds are not at risk in those cases.

> FILE: 2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol 

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

##   [4] CONSTANT REDEFINED ELSEWHERE

###    Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.

##  [5]   It is bad practice to use numbers directly in code without explanation . 

> There are 9  instances of this issue:

> FILE:  2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

          116:    require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

> FILE:  2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol

         127:   require(lpTokenETH.balanceOf(msg.sender) >= 0.5 ether, "No common interest");

> FILE:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

        256:    require(bytes(_newTicker).length >= 3, "String must be 3-5 characters long");

        257:    require(bytes(_newTicker).length <= 5, "String must be 3-5 characters long");

        333:   require(associatedSmartWallet.balance >= 4 ether, "Insufficient balance");

        433:   require(msg.value == len * 4 ether, "Insufficient ether provided");

        431:   require(len >= 1, "No value provided");'

        661:   require(bytes(_stakehouseTicker).length >= 3, "String must be 3-5 characters long");
      
        662: require(bytes(_stakehouseTicker).length <= 5, "String must be 3-5 characters long");


## 

## [6]  Solidity pragma versioning should be upgraded to latest available version. Currently the solidity version in contracts is ^0.8.13 which was found to possess some bugs.  The latest solidity version is 0.8.17 

##

## [7]   INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

> There are 6  instances of this issue:

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

## [8]  SPDX-License-Identifier: STATEMENT SHOULD BE DECLARED BEFORE pragma solidity ^0.8.13; DECLARATIONS . IN MANY INTERFACES LICENSE STATEMENT DECLARED BEFORE PRAGMA STATEMENT . THE BEST CODE PRACTICE IS license DECLARATIONS BEFORE pragma STATEMENT

> There are 9  instances of this issue:

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

##  [9]  LACK OF ZERO CHECKS FOR NEW ADDRESSES IN FUNCTIONS. THERE ARE MANY FUNCTIONS NOT CHECKING ZERO ADDRESS BEFORE ASSIGNING THE VALUES TO VARIABLES. 




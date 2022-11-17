
##  [G1]  ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS. SAME FOR [--I/I--]

### In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline. This saves 30-40 gas per loop

> There are 19 instances of this issue:


                2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

                63:    for (uint256 i; i < numOfRotations; ++i) {

               144    numberOfLPTokensIssued++;

               2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol  

               38:     for (uint256 i; i < numOfVaults; ++i) {

               64:     for (uint256 i; i < numOfVaults; ++i) 

               90 :     for (uint256 i; i < _stakingFundsVaults.length; ++i) {

               117:     for (uint256 i; i < numOfRotations; ++i) {

               135:      for (uint256 i; i < numOfVaults; ++i) {

               183:      for (uint256 i; i < _lpTokens.length; ++i) {

               2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol

               76:        for (uint256 i; i < amountOfTokens; ++i) {

                2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol

               42:      for (uint256 i; i < numOfSavETHVaults; ++i) {

               78:        for (uint256 i; i < numOfVaults; ++i) {

               82:        for (uint256 j; j < _lpTokens[i].length; ++j) { 

               128:       for (uint256 i; i < numOfRotations; ++i) {

              146:        for (uint256 i; i < numOfVaults; ++i) { 

              148:      for (uint256 j; j < _lpTokens[i].length; ++j) { 

              2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

              392:      for(uint256 i; i < _blsPubKeys.length; ++i) { 

             464:        for(uint256 i; i < len; ++i) { 

             537:        for (uint256 i; i < numOfValidators; ++i) { 

             586:       for (uint256 i; i < numOfKnotsToProcess; ++i) { 

            


###

## [G2]   CAN CHECK MORE FAILURE POSSIBILITY CONDITIONS FIRST INSTEAD OF address(0) CHECK . FOR address(0) CONDITIONS FAILURE POSSIBILITY  IS LESS COMPARE TO OTHER require CONDITION CHECKS . IN THIS WAY WE CAN IGNORE  SOME REQUIRE CONDITION CHECKS . CAN SAVE MORE VOLUME OF THE GAS.  WE CAN CHECK address(0) CHECK IN THE LAST 

         FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

        require(address(_oldLPToken) != address(0), "Zero address");
        require(address(_newLPToken) != address(0), "Zero address");
        require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
        require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
        require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

>Proof Of Work:  

        require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
        require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
        require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");
        require(address(_oldLPToken) != address(0), "Zero address");
        require(address(_newLPToken) != address(0), "Zero address");


##

##   [G3]  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> . SAME FOR  <X> -= <Y> COSTS MORE GAS THAN <X> = <X> - <Y>

> There are 5 instances of this issue:

           FILE : 2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

          40:   idleETH -= _ETHTransactionAmounts[i];

          2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol

          39:    idleETH += msg.value;

         57:     idleETH -= _amount;

         46:     idleETH -= transactionAmount;

        2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

        614:     stakedKnotsOfSmartWallet[smartWallet] -= 1; 

       2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol

       65:      totalClaimed += due;

       85:     accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;

      

      

 ##      

##  [G4]   lpTokenETH   State variable can be cached with local stack variables. It will save the gas fee


                 2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

                _distributeETHRewardsToUserForToken(
                msg.sender,
                 address(lpTokenETH),
                 lpTokenETH.balanceOf(msg.sender),
                 _recipient
                  );
         
   ## 

##   [G5]  ADD UNCHECKED {} FOR ARITHMATIC OPERATIONS WHERE THE OPERANDS CANNOT UNDERFLOW        

> There are 3 instances of this issue:    

> 2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

              177:  return address(this).balance + totalClaimed - idleETH;

              203:  claimed[_user][address(lpTokenETH)] = (accumulatedETHPerLPShare * lpTokenETH.balanceOf(_user)) / PRECISION;

               2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

              409:   (uint256 nodeRunnerAmount, uint256 daoAmount) = _calculateCommission(address(this).balance - balBefore);

               2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol

               43:   uint256 newAccumulatedETH = accumulatedETHPerLPShare + ((unprocessed * PRECISION) / _numOfShares);

              45:    return ((newAccumulatedETH * _balanceOfSender) / PRECISION) - claim;

              61: uint256 due = ((accumulatedETHPerLPShare * balance) / PRECISION) - claimed[_user][_token];

             85:   accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;
               
              

##

##   [G6]    DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

###     if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

> There are 14 instances of this issue:   

               2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol 

               require(
                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false, 
                    "ETH is either staked or derivatives minted"
                );

              2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

              291:   require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network"); 

            328 :   require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
 
            332:    require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD 
             network");

            393:    require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network"); 

            435:    require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");  

            436:    require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network"); 

           468:     require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network"); 

          540:       require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

          588:       require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

         687:         require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");


          
 ##

##  [G7]   Instead of using operator && on single require check . Using double require check can save more gas:

> There are 2 instances of this issue: 

               2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

              357:    require(_new != address(0) && _current != _new, "New is zero or current"); 

             371:     if (msg.sender == dao && _wasPreviousNodeRunnerMalicious) { 

Recommended Migration Step : 

             require(_new != address(0),"New is zero");

             require( _current != _new, "New is zero or current"); 

 


##

## [G8]    For || operator don't want to check all conditions . If any one condition true then the overall condition checks become true. Once one condition is true then no need to check remaining conditions. Loss more gas for every condition checks after one condition is  true . 

> There are 2 instances of this issue: 

            2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

           361:  require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");

           500:   return !isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot) || bannedBLSPublicKeys[_blsPublicKeyOfKnot] != address(0);




##  [G9]  FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

###  If a function modifier such as onlyDeployer is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

> There are 2 instances of this issue: 

         2022-11-stakehouse/contracts/liquid-staking/LPToken.sol

        function mint(address _recipient, uint256 _amount) external onlyDeployer {
        _mint(_recipient, _amount);
         }

         /// @notice Allows a LP token owner to burn their tokens
        function burn(address _recipient, uint256 _amount) external onlyDeployer {
         _burn(_recipient, _amount);
        }

##

## [G10]  State variable PRECISION can be catched with stack variable 

> 2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol

        45:    return ((newAccumulatedETH * _balanceOfSender) / PRECISION) - claim;
   
        43:   uint256 newAccumulatedETH = accumulatedETHPerLPShare + ((unprocessed * PRECISION) / _numOfShares);



   



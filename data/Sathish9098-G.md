
##  [G1]  ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS. SAME FOR [--I/I--]

### In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline. This saves 30-40 gas per loop

> There are  instances of this issue:


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

## [G2]   CAN CHECK MORE FAILURE POSSIBILITY CONDITIONS FIRST INSTEAD OF address(0) CHECK . FOR address(0) CONDITIONS FAILURE POSSIBILITY  IS LESS COMPARE TO OTHER require CONDITION CHECKS . IN THIS WAY WE CAN IGNORE  SOME REQUIRE CONDITION CHECKS .CAN SAVE MORE VOLUME OF THE GAS.  WE CAN CHECK address(0) CHECK IN THE LAST 

         FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

        require(address(_oldLPToken) != address(0), "Zero address");
        require(address(_newLPToken) != address(0), "Zero address");
        require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
        require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
        require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

Proof Of Work:  

        require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
        require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
        require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");
        require(address(_oldLPToken) != address(0), "Zero address");
        require(address(_newLPToken) != address(0), "Zero address");


##

##   [G3]  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> . SAME FOR  <X> -= <Y> COSTS MORE GAS THAN <X> = <X> - <Y>

           FILE : 2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

          40:   idleETH -= _ETHTransactionAmounts[i];

          2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol

         39:    idleETH += msg.value;

        57:     idleETH -= _amount;

       46:     idleETH -= transactionAmount;

      2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

       614:     stakedKnotsOfSmartWallet[smartWallet] -= 1; 

       

##  [G4]   STACK VARIABLE USED AS A CHEAPER CACHE FOR A STATE VARIABLE IS ONLY USED ONCE

###     lpTokenETH  can be catched with local address variable. 

               2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

                _distributeETHRewardsToUserForToken(
                msg.sender,
                 address(lpTokenETH),
                 lpTokenETH.balanceOf(msg.sender),
                 _recipient
                  );
         
   ## 

## [G4]  ADD UNCHECKED {} FOR ARITHMATIC OPERATIONS WHERE THE OPERANDS CANNOT UNDERFLOW            

              2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

              177:  return address(this).balance + totalClaimed - idleETH;

              203:  claimed[_user][address(lpTokenETH)] = (accumulatedETHPerLPShare * lpTokenETH.balanceOf(_user)) / PRECISION;

               2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

              409:   (uint256 nodeRunnerAmount, uint256 daoAmount) = _calculateCommission(address(this).balance - balBefore);

##

## [G5]    DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

###     if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

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

##  [G6]   Instead of using operator && on single require check . Using double require check can save more gas:

               2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

              357:    require(_new != address(0) && _current != _new, "New is zero or current"); 

             371:     if (msg.sender == dao && _wasPreviousNodeRunnerMalicious) { 

Recommended Migration Step : 

             require(_new != address(0),"New is zero");

             require( _current != _new, "New is zero or current"); 

 


##

## [G7]    For || operator don't want to check all conditions . If any one condition true then then the overall condition checks become true. Once one condition is become then other condition checks are waste . Loss more gas for every condition checks after one condition true . 

            2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

           361:  require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");

           500:   return !isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot) || bannedBLSPublicKeys[_blsPublicKeyOfKnot] != address(0);

Recommended Migration Step : 

           We can use If elseif condition checks to avoid this issue . 





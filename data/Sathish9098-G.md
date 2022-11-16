
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

##   [G3]  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES. SAME FOR  <X> -= <Y> COSTS MORE GAS THAN <X> = <X> - <Y>

           FILE : 2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

          40:   idleETH -= _ETHTransactionAmounts[i];

          2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol

         39:    idleETH += msg.value;

        57:     idleETH -= _amount;

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
               

##  [G1]  ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS. SAME FOR [--I/I--]

### In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline. This saves 30-40 gas per loop

> There are 40 instances of this issue:


> FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

                63:    for (uint256 i; i < numOfRotations; ++i) {

               144    numberOfLPTokensIssued++;

> FILE: 2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol  

               38:     for (uint256 i; i < numOfVaults; ++i) {

               64:     for (uint256 i; i < numOfVaults; ++i) 

               90 :     for (uint256 i; i < _stakingFundsVaults.length; ++i) {

               117:     for (uint256 i; i < numOfRotations; ++i) {

              135:      for (uint256 i; i < numOfVaults; ++i) {

              183:      for (uint256 i; i < _lpTokens.length; ++i) {

>FILE:   2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol

               76:        for (uint256 i; i < amountOfTokens; ++i) {

>FILE:   2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol

                42:      for (uint256 i; i < numOfSavETHVaults; ++i) {

                78:        for (uint256 i; i < numOfVaults; ++i) {

                82:        for (uint256 j; j < _lpTokens[i].length; ++j) { 

               128:       for (uint256 i; i < numOfRotations; ++i) {

              146:        for (uint256 i; i < numOfVaults; ++i) { 

              148:      for (uint256 j; j < _lpTokens[i].length; ++j) { 

> FILE:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

              392:      for(uint256 i; i < _blsPubKeys.length; ++i) { 

             464:        for(uint256 i; i < len; ++i) { 

             538:        for (uint256 i; i < numOfValidators; ++i) { 

             587:       for (uint256 i; i < numOfKnotsToProcess; ++i) { 

>   2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol

             78:   for (uint256 i; i < numOfValidators; ++i) {

            152:    for (uint256 i; i < numOfTokens; ++i) {

            166:    for (uint256 i; i < numOfTokens; ++i) {

            203:    for (uint256 i; i < _blsPubKeys.length; ++i) {

            266:    for (uint256 i; i < _blsPublicKeys.length; ++i) {

           276:     for (uint256 i; i < _blsPubKeys.length; ++i) {

           286:    for (uint256 i; i < _token.length; ++i) {

>FILE:   2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol

           63:    for (uint256 i; i < numOfValidators; ++i) {

          103:    for (uint256 i; i < numOfTokens; ++i) {

          116  :   for (uint256 i; i < numOfTokens; ++i) {

> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

          211:   for (uint256 i; i < _blsPubKeys.length; ++i) {

          259:    for (uint256 i; i < _blsPubKeys.length; ++i) {

         301:     for (uint256 i; i < _blsPubKeys.length; ++i) {

         346:     for (uint256 i; i < _blsPubKeys.length; ++i) {

         420:     for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

         513:     for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {

        560:     for (uint256 i; i < knotsToRegister; ++i) {

       585:     for (uint256 i; i < _priorityStakers.length; ++i) {

       598:    for (uint256 i; i < _blsPublicKeys.length; ++i) {

       648:    for (uint256 i; i < _blsPubKeys.length; ++i) {

       
###

##  [G2]   CAN CHECK MORE FAILURE POSSIBILITY CONDITIONS FIRST INSTEAD OF address(0) CHECK . FOR address(0) CONDITIONS FAILURE POSSIBILITY  IS LESS COMPARE TO OTHER require CONDITION CHECKS . IN THIS WAY WE CAN IGNORE  SOME REQUIRE CONDITION CHECKS . CAN SAVE MORE VOLUME OF THE GAS.  WE CAN CHECK address(0) CHECK IN THE LAST 

> FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

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

> There are 30 instances of this issue:

 > FILE : 2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

            40:   idleETH -= _ETHTransactionAmounts[i];

 > FILE: 2022-11-stakehouse/contracts/liquid-staking/GiantPoolBase.sol

          39:    idleETH += msg.value;

          57:     idleETH -= _amount;

          46:     idleETH -= transactionAmount;

> FILE:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

        614:     stakedKnotsOfSmartWallet[smartWallet] -= 1; 

        839:     numberOfKnots += 1;

       782:      numberOfKnots += 1;

       770:        stakedKnotsOfSmartWallet[smartWallet] += 1;

       615:       stakedKnotsOfSmartWallet[smartWallet] -= 1;

> FILE:  2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol

       65:      totalClaimed += due;

       85:     accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;

> FILE:   2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol

            58:   totalShares += 4 ether;

            97:  totalAmount += amount;

           278:   totalAccumulated += previewAccumulatedETH(_user, token);

           287:   totalAccumulated += previewAccumulatedETH(_user, _token[i]);

 > FILE:  2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol

           71:     totalAmount += amount;

> FILE:  2022-11-stakehouse/contracts/syndicate/Syndicate.sol

         185:    accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);

         190:      accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;

         226:     sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;

         227:     sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;

         269:    totalFreeFloatingShares -= _sETHAmount;

         272:   sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;

         273:    sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;]]];

         317:     totalClaimed += unclaimedUserShare;

         430:     currentAccrued +=
                        balance * unprocessedForKnot / (4 ether - currentSlashedAmount);

        511:     accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;

       521:      accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
                            balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);

       538:    numberOfRegisteredKnots += knotsToRegister;

       658:    totalClaimed += unclaimedUserShare;

##      

##  [G4]   lpTokenETH   State variable can be cached with local stack variables. It will save the gas fee


 > FILE:  2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

                _distributeETHRewardsToUserForToken(
                msg.sender,
                 address(lpTokenETH),
                 lpTokenETH.balanceOf(msg.sender),
                 _recipient
                  );
         
   ## 

##   [G5]  ADD UNCHECKED {} FOR ARITHMATIC OPERATIONS WHERE THE OPERANDS CANNOT UNDERFLOW    

<https://docs.soliditylang.org/en/v0.8.7/control-structures.html#checked-or-unchecked-arithmetic>    

> There are 24 instances of this issue:    

> FILE:  2022-11-stakehouse/contracts/liquid-staking/GiantMevAndFeesPool.sol

              177:  return address(this).balance + totalClaimed - idleETH;

              203:  claimed[_user][address(lpTokenETH)] = (accumulatedETHPerLPShare * lpTokenETH.balanceOf(_user)) / PRECISION;

> FILE:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

              409:   (uint256 nodeRunnerAmount, uint256 daoAmount) = _calculateCommission(address(this).balance - balBefore);

              925:    uint256 daoAmount = (_received * daoCommissionPercentage) / MODULO;

> FILE:  2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol

               43:   uint256 newAccumulatedETH = accumulatedETHPerLPShare + ((unprocessed * PRECISION) / _numOfShares);

              45:    return ((newAccumulatedETH * _balanceOfSender) / PRECISION) - claim;

              61: uint256 due = ((accumulatedETHPerLPShare * balance) / PRECISION) - claimed[_user][_token];

             85:   accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;

>FILE: 2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol

             103:  claimed[msg.sender][address(tokenForKnot)] = (tokenForKnot.balanceOf(msg.sender) * accumulatedETHPerLPShare) / PRECISION;

             140:   claimed[msg.sender][address(tokenForKnot)] = (tokenForKnot.balanceOf(msg.sender) * accumulatedETHPerLPShare) / PRECISION;

> FILE:    2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol

             174:   redemptionValue = (dETHDetails.savETHBalance * _amount) / 24 ether;

> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

            189:     uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);

            312:     uint256 unclaimedUserShare = userShare - claimedPerCollateralizedSlotOwnerOfKnot[_blsPubKey][msg.sender];

            369:      return userShare - sETHUserClaimForKnot[_blsPubKey][_user];

            395:        return userShare - sETHUserClaimForKnot[_blsPubKey][_staker];

            406:        uint256 accumulatedSoFar = accumulatedETHPerCollateralizedSlotPerKnot
                    + ((calculateETHForFreeFloatingOrCollateralizedHolders() - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);

            409:       uint256 unprocessedForKnot = accumulatedSoFar - totalETHProcessedPerCollateralizedKnot[_blsPubKey];

            437:      return currentAccrued - claimedPerCollateralizedSlotOwnerOfKnot[_blsPubKey][_staker];

           442:      return calculateETHForFreeFloatingOrCollateralizedHolders() - lastSeenETHPerFreeFloating;

           447:    return ((calculateETHForFreeFloatingOrCollateralizedHolders() - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);

           493:    uint256 unprocessedETHForCurrentKnot =
                    accumulatedETHPerCollateralizedSlotPerKnot - totalETHProcessedPerCollateralizedKnot[_blsPubKey];

           621:     totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];

           624:    numberOfRegisteredKnots -= 1;

##

##   [G6]    DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

###     if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

> There are 18 instances of this issue:   

> FILE:   2022-11-stakehouse/contracts/liquid-staking/GiantSavETHVaultPool.sol 

               require(
                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false, 
                    "ETH is either staked or derivatives minted"
                );

> FILE:   2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

              291:   require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network"); 

            328 :   require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");
 
            332:    require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD 
             network");

            393:    require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network"); 

            435:    require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");  

            436:    require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network"); 

            468:     require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network"); 

           540:       require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

           589:       require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");

           687:         require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

>FILE:   2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol

         79:   require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

          require(
                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,
                "Unknown BLS public key"
            );

          114:   require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of 
                     LSD network");

> FILE:  2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol

           64:   require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

          84:    require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD 
         network");

> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

         622:   if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();

         623:    if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();

 ##

##  [G7]   Instead of using operator && on single require check . Using multiple require of if checks can save more gas: 

> There are 9 instances of this issue: 

> FILE:   2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

             357:    require(_new != address(0) && _current != _new, "New is zero or current"); 

             371:     if (msg.sender == dao && _wasPreviousNodeRunnerMalicious) {

             717:     else if(_isEnabled && smartWalletRepresentative[_smartWallet] == address(0)) {

             700:     if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) {  

> FILE:   2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol

             215:  if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) {

> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

             218:   if (block.number < priorityStakingEndBlock && !isPriorityStaker[_onBehalfOf]) revert NotPriorityStaker();

            500:    if (unprocessedETHForCurrentKnot > 0 && !isNoLongerPartOfSyndicate[_blsPubKey]) {

            533:      if (!isActive && !isNoLongerPartOfSyndicate[_blsPubKey]) {

           588:     if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();

           
Recommended Migration Step : 

  POSSIBLE TO SPLIT THE REQUIRE OR IF CONDITIONS. TO SAVE MORE GAS FEE . IF FIRST CONDITION FAILED THEN OTHER CONDITION CHECKS SKIPED . 

             require(_new != address(0),"New is zero");

             require( _current != _new, "New is zero or current"); 

          
##

## [G8]    For || operator don't want to check all conditions . If any one condition true then the overall condition checks become true. Once one condition is true then no need to check remaining conditions. Loss more gas for every condition checks after one condition is  true . 

> There are 4 instances of this issue: 

> FILE:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

           361:  require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");

           500:   return !isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot) || bannedBLSPublicKeys[_blsPublicKeyOfKnot] != address(0);

>FILE:    2022-11-stakehouse/contracts/liquid-staking/SavETHVault.sol

            require(
            validatorStatus == IDataStructures.LifecycleStatus.INITIALS_REGISTERED ||
            validatorStatus == IDataStructures.LifecycleStatus.TOKENS_MINTED,
            "Cannot burn LP tokens"
            );

> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

           216:  if (!isKnotRegistered[_blsPubKey] || isNoLongerPartOfSyndicate[_blsPubKey]) revert KnotIsNotRegisteredWithSyndicate();

##

##  [G9]  FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

###  If a function modifier such as onlyDeployer, onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

> There are 5 instances of this issue: 

> FILE:  2022-11-stakehouse/contracts/liquid-staking/LPToken.sol

        function mint(address _recipient, uint256 _amount) external onlyDeployer {
        _mint(_recipient, _amount);
         }

         /// @notice Allows a LP token owner to burn their tokens
        function burn(address _recipient, uint256 _amount) external onlyDeployer {
         _burn(_recipient, _amount);
        }


> FILE:  2022-11-stakehouse/contracts/syndicate/Syndicate.sol

      function registerKnotsToSyndicate(
        bytes[] calldata _newBLSPublicKeyBeingRegistered
      ) external onlyOwner {


     function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {
        updateAccruedETHPerShares();
        _deRegisterKnots(_blsPublicKeys);
    }

     function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
        updateAccruedETHPerShares();
        priorityStakingEndBlock = _endBlock;
    }


##

## [G10]  State variable PRECISION can be cached with stack variable 

> FILE:  2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol

        45:    return ((newAccumulatedETH * _balanceOfSender) / PRECISION) - claim;  // @Audit PRECISION
   
        43:   uint256 newAccumulatedETH = accumulatedETHPerLPShare + ((unprocessed * PRECISION) / _numOfShares);   // @Audit PRECISION


##

## [G11]  State variable totalETHSeen can be cached with stack variable 

>FILE:  2022-11-stakehouse/contracts/liquid-staking/SyndicateRewardsProcessor.sol

          79:    uint256 unprocessed = received - totalETHSeen;   // @Audit totalETHSeen

          87:    totalETHSeen = received;    // @Audit totalETHSeen
 
##

##   [G12]   State variable  liquidStakingNetworkManager  can be cached with stack variable 

>FILE:    2022-11-stakehouse/contracts/liquid-staking/StakingFundsVault.sol

                require(
                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,     // @Audit liquidStakingNetworkManager
                "Unknown BLS public key"
                );

               215:    if (i == 0 && !Syndicate(payable(liquidStakingNetworkManager.syndicate())).isNoLongerPartOfSyndicate(_blsPubKeys[i])) { 

  // @Audit liquidStakingNetworkManager


              _claimFundsFromSyndicateForDistribution(
                    liquidStakingNetworkManager.syndicate(),    // @Audit liquidStakingNetworkManager
                    _blsPubKeys
                );

##

## [G13]   IF WE NEED TO INCREASE OR DECREASE STATE VARIBALE BY 1 WE CAN USE ++X OR --X   INSTEAD OF (X)+= 1  OR (X)- = 1  . IT WILL COST MORE GAS FEE. IN THIS WAY WE CAN SAVE 226 gas FOR EVERY EXPRESSIONS .

> There are 6 instances of this issue: 

> FILE:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

        614:     stakedKnotsOfSmartWallet[smartWallet] -= 1; 

        839:     numberOfKnots += 1;

       782:      numberOfKnots += 1;

       770:        stakedKnotsOfSmartWallet[smartWallet] += 1;

       615:       stakedKnotsOfSmartWallet[smartWallet] -= 1;

> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

       624:    numberOfRegisteredKnots -= 1;

##  

## G[14]    In _joinLSDNStakehouse() function **brand** should be cached with stack variable 

> File:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

> See @audit

<https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L789-L797>

       function _joinLSDNStakehouse(
        bytes calldata _blsPubKey,
        IDataStructures.ETH2DataReport calldata _beaconChainBalanceReport,
        IDataStructures.EIP712Signature calldata _reportSignature
        ) internal {
        // total number of knots created with the syndicate increases
        numberOfKnots += 1;

        // The savETH will go to the savETH vault, the collateralized SLOT for syndication owned by the smart wallet
        // sETH will also be minted in the smart wallet but will be moved out and distributed to the syndicate for claiming by the DAO
        address associatedSmartWallet = smartWalletOfKnot[_blsPubKey];

        // Join the LSDN stakehouse
        string memory lowerTicker = IBrandNFT(brand).toLowerCase(stakehouseTicker);        ///@audit brand 
        IOwnableSmartWallet(associatedSmartWallet).execute(
            address(getTransactionRouter()),
            abi.encodeWithSelector(
                ITransactionRouter.joinStakehouse.selector,
                associatedSmartWallet,
                _blsPubKey,
                stakehouse,
                IBrandNFT(brand).lowercaseBrandTickerToTokenId(lowerTicker),      ///@audit brand 
                savETHVault.indexOwnedByTheVault(),
                _beaconChainBalanceReport,
                _reportSignature
            )
        );

        // Register the knot to the syndicate
        bytes[] memory _blsPublicKeyOfKnots = new bytes[](1);
        _blsPublicKeyOfKnots[0] = _blsPubKey;
        Syndicate(payable(syndicate)).registerKnotsToSyndicate(_blsPublicKeyOfKnots);

        // Autostake DAO sETH with the syndicate
        _autoStakeWithSyndicate(associatedSmartWallet, _blsPubKey);

        emit StakehouseJoined(_blsPubKey);
      }

##

## G[15]   In _createLSDNStakehouse() function **stakehouse** state variable should be cached with local variable . stakehouse state variable used 5 times in this function instead stack variables.

<https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L816-L876 >

> File:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

> See @audit 

       function _createLSDNStakehouse(
        bytes calldata _blsPublicKeyOfKnot,
        IDataStructures.ETH2DataReport calldata _beaconChainBalanceReport,
        IDataStructures.EIP712Signature calldata _reportSignature
       ) internal {
        // create stakehouse and mint derivative for first bls key - the others are just used to create the syndicate
        // The savETH will go to the savETH vault, the collateralized SLOT for syndication owned by the smart wallet
        // sETH will also be minted in the smart wallet but will be moved out and distributed to the syndicate for claiming by the DAO
        address associatedSmartWallet = smartWalletOfKnot[_blsPublicKeyOfKnot];
        IOwnableSmartWallet(associatedSmartWallet).execute(
            address(getTransactionRouter()),
            abi.encodeWithSelector(
                ITransactionRouter.createStakehouse.selector,
                associatedSmartWallet,
                _blsPublicKeyOfKnot,
                stakehouseTicker,
                savETHVault.indexOwnedByTheVault(),
                _beaconChainBalanceReport,
                _reportSignature
            )
        );

        // Number of knots has increased
        numberOfKnots += 1;

        // Capture the address of the Stakehouse for future knots to join
        stakehouse = getStakeHouseUniverse().memberKnotToStakeHouse(_blsPublicKeyOfKnot);                        //@Audit  stakehouse
        IERC20 sETH = IERC20(getSlotRegistry().stakeHouseShareTokens(stakehouse));                                     //@Audit  stakehouse

        // Give liquid staking manager ability to manage keepers and set a house keeper if decided by the network
        IOwnableSmartWallet(associatedSmartWallet).execute(
            stakehouse,                                                                        //@Audit  stakehouse
            abi.encodeWithSelector(
                Ownable.transferOwnership.selector,
                address(this)
            )
        );

        if (address(gatekeeper) != address(0)) {
            IStakeHouseRegistry(stakehouse).setGateKeeper(address(gatekeeper));                            //@Audit  stakehouse
        }

        // Deploy the EIP1559 transaction reward sharing contract but no priority required because sETH will be auto staked
        address[] memory priorityStakers = new address[](0);
        bytes[] memory initialKnots = new bytes[](1);
        initialKnots[0] = _blsPublicKeyOfKnot;
        syndicate = syndicateFactory.deploySyndicate(
            address(this),
            0,
            priorityStakers,
            initialKnots
        );

        // Contract approves syndicate to take sETH on behalf of the DAO
        sETH.approve(syndicate, (2 ** 256) - 1);

        // Auto-stake sETH by pulling sETH out the smart wallet and staking in the syndicate
        _autoStakeWithSyndicate(associatedSmartWallet, _blsPublicKeyOfKnot);

        emit StakehouseCreated(stakehouseTicker, stakehouse);                                     //@Audit  stakehouse
    }

##

## [G16]    _createLSDNStakehouse()  **syndicate** state variable should be cached . state syndicate used two times 

<https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L816-L876 >

> File:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

> See @audit 

        address[] memory priorityStakers = new address[](0);
        bytes[] memory initialKnots = new bytes[](1);
        initialKnots[0] = _blsPublicKeyOfKnot;
        syndicate = syndicateFactory.deploySyndicate(             /// @Audit  syndicate 
            address(this),
            0,
            priorityStakers,
            initialKnots
        );

        // Contract approves syndicate to take sETH on behalf of the DAO
        sETH.approve(syndicate, (2 ** 256) - 1);                /// @Audit  syndicate 
            address(this),
 
##

## [G17] State varibale **dao** should be cached with stack variable 

> File:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

> See @Audit

      function updateDAOAddress(address _newAddress) external onlyDAO {
        require(_newAddress != address(0), "Zero address");
        require(_newAddress != dao, "Same address");  // @Audit dao 

        emit UpdateDAOAddress(dao, _newAddress);  // @Audit dao 

        dao = _newAddress;  // @Audit dao 
    }


       function rotateNodeRunnerOfSmartWallet(address _current, address _new, bool _wasPreviousNodeRunnerMalicious) external {
        require(_new != address(0) && _current != _new, "New is zero or current");

        address wallet = smartWalletOfNodeRunner[_current];
        require(wallet != address(0), "Wallet does not exist");
        require(_current == msg.sender || dao == msg.sender, "Not current owner or DAO");    // @Audit dao 

        address newRunnerCurrentWallet = smartWalletOfNodeRunner[_new];
        require(newRunnerCurrentWallet == address(0), "New runner has a wallet");

        smartWalletOfNodeRunner[_new] = wallet;
        nodeRunnerOfSmartWallet[wallet] = _new;

        delete smartWalletOfNodeRunner[_current];

        if (msg.sender == dao && _wasPreviousNodeRunnerMalicious) {  // @Audit dao 
            bannedNodeRunners[_current] = true;
            emit NodeRunnerBanned(_current);
        }

        emit NodeRunnerOfSmartWalletRotated(wallet, _current, _new);
    }

##

## [G18]  State variable gatekeeper should be cached 

> File:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

> See @audit 

<https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L854-L856>

##

##   [G19]  State variable enableWhitelisting should be cached . We can use stack variable instead of call state variable 

> File:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

> @See @Audit

       function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {
        require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status"); // @Audit enableWhitelisting
        enableWhitelisting = _changeWhitelist;                                                       // @Audit enableWhitelisting
        emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);                 // @Audit enableWhitelisting

        return enableWhitelisting;                                                                                 // @Audit enableWhitelisting
        } 

##

## G[20]   State variable daoCommissionPercentage  should be cached . We can use stack variable instead of call state variable 

> File:  2022-11-stakehouse/contracts/liquid-staking/LiquidStakingManager.sol

> @See @Audit

       function _calculateCommission(uint256 _received) internal virtual view returns (uint256 _nodeRunner, uint256 _dao) {
        require(_received > 0, "Nothing received");

        if (daoCommissionPercentage > 0) {                      // @Audit daoCommissionPercentage 
            uint256 daoAmount = (_received * daoCommissionPercentage) / MODULO;            // @Audit daoCommissionPercentage
            uint256 rest = _received - daoAmount;
            return (rest, daoAmount);
        }

        return (_received, 0);
      }

      function _updateDAORevenueCommission(uint256 _commissionPercentage) internal {
        require(_commissionPercentage <= MODULO, "Invalid commission");

        emit DAOCommissionUpdated(daoCommissionPercentage, _commissionPercentage);  // @Audit daoCommissionPercentage

        daoCommissionPercentage = _commissionPercentage;          // @Audit daoCommissionPercentage
    }

##

##   G[21]   calldata can be used instead of memory. To save a gas cost .


> There are 6 instance for this problem:  
   
> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

        337:     function updateCollateralizedSlotOwnersAccruedETH(bytes memory _blsPubKey) external {

       343:     function batchUpdateCollateralizedSlotOwnersAccruedETH(bytes[] memory _blsPubKeys) external {

      359:     function calculateUnclaimedFreeFloatingETHShare(bytes memory _blsPubKey, address _user) public view returns (uint256) {


        469:    function _initialize(
          address _contractOwner,
        uint256 _priorityStakingEndBlock,
        address[] memory _priorityStakers,
        bytes[] memory _blsPubKeysForSyndicateKnots
        ) internal {


     491:   function _updateCollateralizedSlotOwnersLiabilitySnapshot(bytes memory _blsPubKey) internal {

    555:    function _registerKnotsToSyndicate(bytes[] memory _blsPubKeysForSyndicateKnots) internal {

 

##

##   [G22]    DIVISION BY TWO SHOULD USE BIT SHIFTING

###   <x> / 2 is the same as <x> >> 1. While the compiler uses the SHR opcode to accomplish both, the version that uses division incurs an overhead of 20 gas due to JUMPs to and from a compiler utility function that introduces checks which can be avoided by using unchecked {} around the division by two

> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

       378:     return ethPerKnot / 2;

##

##  [G23] State variable totalFreeFloatingShares should be cached with stack variable

> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

> See @audit 


       function _calculateNewAccumulatedETHPerFreeFloatingShare(uint256 _ethSinceLastUpdate) internal view returns (uint256) {
        return totalFreeFloatingShares > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingShares : 0;  //@Audit  totalFreeFloatingShares 
        }

##

##   [G24]   State variable numberOfRegisteredKnots should be cached with stack variable

> FILE: 2022-11-stakehouse/contracts/syndicate/Syndicate.sol

> See @audit 

         function updateAccruedETHPerShares() public {
        // Ensure there are registered KNOTs. Syndicates are deployed with at least 1 registered but this can fall to zero.
        // Fee recipient should be re-assigned in the event that happens as any further ETH can be collected by owner
        if (numberOfRegisteredKnots > 0) {                                           //@Audit  numberOfRegisteredKnots 
            // All time, total ETH that was earned per slot type (free floating or collateralized)
            uint256 totalEthPerSlotType = calculateETHForFreeFloatingOrCollateralizedHolders();

            // Process free floating if there are staked shares
            uint256 freeFloatingUnprocessed;
            if (totalFreeFloatingShares > 0) {
                freeFloatingUnprocessed = getUnprocessedETHForAllFreeFloatingSlot();
                accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);
                lastSeenETHPerFreeFloating = totalEthPerSlotType;
            }

            uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);  

             //@Audit  numberOfRegisteredKnots 

            accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
            lastSeenETHPerCollateralizedSlotPerKnot = totalEthPerSlotType;

            emit UpdateAccruedETH(freeFloatingUnprocessed + collateralizedUnprocessed);
        } else {
            // todo - check else case for any ETH lost
        }
    }

##

## G[25]  <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
      
The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

> There are 40 instances of this issue:

##

## G[25]  OPTIMIZE NAMES TO SAVE GAS

   ###  public/external function names and public member variable names can be optimized to save gas. 
           renaming functions to have lower method IDs will save 22 gas per call

> So Many Functions and state variable names are so big . If we optimize the function and variable names can save the gas fee.

##

## G[26]  EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING

    Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

2022-11-stakehouse/contracts/syndicate/Syndicate.sol

receive() external payable {
        // No logic here because one cannot assume that more than 21K GAS limit is forwarded
    }



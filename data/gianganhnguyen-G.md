# 1. [G-1] For loops: increments in for loop can be uncheck to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

    File contracts/liquid-staking/ETHPoolLPFactory.sol, line 63:     for (uint256 i; i < numOfRotations; ++i)

    File contracts/liquid-staking/GiantMevAndFeesPool.sol, line 38:     for (uint256 i; i < numOfVaults; ++i)
    File contracts/liquid-staking/GiantMevAndFeesPool.sol, line 64:     for (uint256 i; i < numOfVaults; ++i)
    File contracts/liquid-staking/GiantMevAndFeesPool.sol, line 90:     for (uint256 i; i < _stakingFundsVaults.length; ++i)
    File contracts/liquid-staking/GiantMevAndFeesPool.sol, line 117:     for (uint256 i; i < numOfRotations; ++i)
    File contracts/liquid-staking/GiantMevAndFeesPool.sol, line 135:     for (uint256 i; i < numOfVaults; ++i)
    File contracts/liquid-staking/GiantMevAndFeesPool.sol, line 183:     for (uint256 i; i < _lpTokens.length; ++i)

    File contracts/liquid-staking/GiantPoolBase.sol, line 76:     for (uint256 i; i < amountOfTokens; ++i)

    File contracts/liquid-staking/GiantSavETHVaultPool.sol, line 42, 78, 82, 128, 146, 148.

    File contracts/liquid-staking/LiquidStakingManager.sol, line 538, 587.
     
    File contracts/liquid-staking/SavETHVault.sol, line 63, 103, 116.
     
    File contracts/liquid-staking/StakingFundsVault.sol, line 78, 152, 166, 203, 266, 276, 286.
     
    File contracts/syndicate/Syndicate.sol, line 211, 259, 301, 346, 420, 513, 560, 595, 598, 648.

The code would go from:

    for (uint256 i; i < numIterations; i++) { 
      ...
    }

to:

    for (uint256 i; i < numIterations;) { 
      ...
      unchecked { ++i; }  
    }

# 2. [G-2] Arithmetics: uncheck blocks for arithmetics operations that can't underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible, some gas can be saved by using an `unchecked` block.

I suggest wrapping with an `unchecked` block here:

    File contracts/liquid-staking/GiantMevAndFeesPool.sol, line 177:     return address(this).balance + totalClaimed - idleETH;

    File contracts/liquid-staking/GiantPoolBase.sol, line 57:     idleETH -= _amount;

    File contracts/liquid-staking/GiantSavETHVaultPool.sol, line 46:     idleETH -= transactionAmount;

    File contracts/liquid-staking/LiquidStakingManager.sol, line 615:     stakedKnotsOfSmartWallet[smartWallet] -= 1;
    File contracts/liquid-staking/LiquidStakingManager.sol, line 782:     numberOfKnots += 1;
    File contracts/liquid-staking/LiquidStakingManager.sol, line 839:     numberOfKnots += 1;
    File contracts/liquid-staking/LiquidStakingManager.sol, line 925 - 926:     
                        uint256 daoAmount = (_received * daoCommissionPercentage) / MODULO;
                        uint256 rest = _received - daoAmount;

    File contracts/liquid-staking/SavETHVault.sol, line 71:     totalAmount += amount;

    File contracts/liquid-staking/StakingFundsVault.sol, line 58:     totalShares += 4 ether;
    File contracts/liquid-staking/StakingFundsVault.sol, line 97:     totalAmount += amount;
    File contracts/liquid-staking/StakingFundsVault.sol, line 278:     totalAccumulated += previewAccumulatedETH(_user, token);
    File contracts/liquid-staking/StakingFundsVault.sol, line 287:     totalAccumulated += previewAccumulatedETH(_user, _token[i]);

    File contracts/liquid-staking/SyndicateRewardsProcessor.sol, line 40 - 45:     
                        uint256 received = totalRewardsReceived() + _unclaimedETHFromSyndicate;
                        uint256 unprocessed = received - totalETHSeen;

                        uint256 newAccumulatedETH = accumulatedETHPerLPShare + ((unprocessed * PRECISION) / _numOfShares);

                        return ((newAccumulatedETH * _balanceOfSender) / PRECISION) - claim;

    File contracts/liquid-staking/SyndicateRewardsProcessor.sol, line 61:     uint256 due = ((accumulatedETHPerLPShare * balance) / PRECISION) - claimed[_user][_token];
    File contracts/liquid-staking/SyndicateRewardsProcessor.sol, line 65:     totalClaimed += due;
    File contracts/liquid-staking/SyndicateRewardsProcessor.sol, line 85:     accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;
    File contracts/liquid-staking/SyndicateRewardsProcessor.sol, line 94:     return address(this).balance + totalClaimed;

    File contracts/syndicate/Syndicate.sol, line 185:     accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);

    File contracts/syndicate/Syndicate.sol, line 189 - 190:     
                        uint256 collateralizedUnprocessed = ((totalEthPerSlotType - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);
                        accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;

    File contracts/syndicate/Syndicate.sol, line 225 - 228:     
                        totalFreeFloatingShares += _sETHAmount;
                        sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
                        sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
                        sETHUserClaimForKnot[_blsPubKey][_onBehalfOf] = (_sETHAmount * accumulatedETHPerFreeFloatingShare) / PRECISION;

    File contracts/syndicate/Syndicate.sol, line 269:     totalFreeFloatingShares -= _sETHAmount;
    File contracts/syndicate/Syndicate.sol, line 272 - 273:     
                        sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
                        sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;

    File contracts/syndicate/Syndicate.sol, line 312:     uint256 unclaimedUserShare = userShare - claimedPerCollateralizedSlotOwnerOfKnot[_blsPubKey][msg.sender];
    File contracts/syndicate/Syndicate.sol, line 317:     totalClaimed += unclaimedUserShare;
    File contracts/syndicate/Syndicate.sol, line 366:     uint256 userShare = (accumulatedETHPerShare * stakedBal) / PRECISION;
    File contracts/syndicate/Syndicate.sol, line 369:     return userShare - sETHUserClaimForKnot[_blsPubKey][_user];
    File contracts/syndicate/Syndicate.sol, line 378:     return ethPerKnot / 2;
    File contracts/syndicate/Syndicate.sol, line 393 - 395:     
                    uint256 userShare = (updatedAccumulatedETHPerFreeFloatingShare * stakedBal) / PRECISION;

                    return userShare - sETHUserClaimForKnot[_blsPubKey][_staker];

    File contracts/syndicate/Syndicate.sol, line 406 - 409:     
                    uint256 accumulatedSoFar = accumulatedETHPerCollateralizedSlotPerKnot
                                + ((calculateETHForFreeFloatingOrCollateralizedHolders() - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);

                    uint256 unprocessedForKnot = accumulatedSoFar - totalETHProcessedPerCollateralizedKnot[_blsPubKey];

    File contracts/syndicate/Syndicate.sol, line 430:     currentAccrued +=
                        balance * unprocessedForKnot / (4 ether - currentSlashedAmount);

    File contracts/syndicate/Syndicate.sol, line 437:     return currentAccrued - claimedPerCollateralizedSlotOwnerOfKnot[_blsPubKey][_staker];
    File contracts/syndicate/Syndicate.sol, line 460:     return accumulatedETHPerCollateralizedSlotPerKnot + ethSinceLastUpdate;
    File contracts/syndicate/Syndicate.sol, line 465:     return address(this).balance + totalClaimed;
    File contracts/syndicate/Syndicate.sol, line 493:     uint256 unprocessedETHForCurrentKnot =
                    accumulatedETHPerCollateralizedSlotPerKnot - totalETHProcessedPerCollateralizedKnot[_blsPubKey];
    File contracts/syndicate/Syndicate.sol, line 521:     accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
                            balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);
    File contracts/syndicate/Syndicate.sol, line 540:     uint256 collateralizedSLOTShareOfETHPerKnot = (collateralizedSLOTShareOfETH / numberOfRegisteredKnots);
    File contracts/syndicate/Syndicate.sol, line 546:     return (_ethSinceLastUpdate * PRECISION) / (numberOfRegisteredKnots * 4 ether);
    File contracts/syndicate/Syndicate.sol, line 551:     return totalFreeFloatingShares > 0 ? (_ethSinceLastUpdate * PRECISION) / totalFreeFloatingShares : 0;
    File contracts/syndicate/Syndicate.sol, line 558:     numberOfRegisteredKnots += knotsToRegister;
    File contracts/syndicate/Syndicate.sol, line 621 - 624:     
                    totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];

                    // Total number of registered knots with the syndicate reduces by one
                    numberOfRegisteredKnots -= 1;

    File contracts/syndicate/Syndicate.sol, line 658:     totalClaimed += unclaimedUserShare;
    File contracts/syndicate/Syndicate.sol, line 664:     sETHUserClaimForKnot[_blsPubKey][msg.sender] =
                (accumulatedETHPerShare * sETHStakedBalanceForKnot[_blsPubKey][msg.sender]) / PRECISION;

# 3. [G-3] <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

There are 23 instances of this issue:

    File contracts/liquid-staking/GiantPoolBase.sol, line 57:     idleETH -= _amount;

    File contracts/liquid-staking/GiantSavETHVaultPool.sol, line 46:     idleETH -= transactionAmount;
    
    File contracts/liquid-staking/SavETHVault.sol, line 71:     totalAmount += amount;

    File contracts/liquid-staking/StakingFundsVault.sol, line 58:     totalShares += 4 ether;
    File contracts/liquid-staking/StakingFundsVault.sol, line 97:     totalAmount += amount;
    File contracts/liquid-staking/StakingFundsVault.sol, line 278:     totalAccumulated += previewAccumulatedETH(_user, token);
    File contracts/liquid-staking/StakingFundsVault.sol, line 287:     totalAccumulated += previewAccumulatedETH(_user, _token[i]);
    
    File contracts/liquid-staking/SyndicateRewardsProcessor.sol, line 65:     totalClaimed += due;
    File contracts/liquid-staking/SyndicateRewardsProcessor.sol, line 85:     accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;
    
    File contracts/syndicate/Syndicate.sol, line 185:     accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);

    File contracts/syndicate/Syndicate.sol, line 190:     
                        accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
    File contracts/syndicate/Syndicate.sol, line 225 - 227:     
                        totalFreeFloatingShares += _sETHAmount;
                        sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
                        sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
                        
    File contracts/syndicate/Syndicate.sol, line 269:     totalFreeFloatingShares -= _sETHAmount;
    File contracts/syndicate/Syndicate.sol, line 272 - 273:     
                        sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
                        sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;
                        
    File contracts/syndicate/Syndicate.sol, line 317:     totalClaimed += unclaimedUserShare;
    
    File contracts/syndicate/Syndicate.sol, line 430:     currentAccrued +=
                        balance * unprocessedForKnot / (4 ether - currentSlashedAmount);
                        
    File contracts/syndicate/Syndicate.sol, line 521:     accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
    balance * unprocessedETHForCurrentKnot / (4 ether - currentSlashedAmount);
    
    File contracts/syndicate/Syndicate.sol, line 558:     numberOfRegisteredKnots += knotsToRegister;

    File contracts/syndicate/Syndicate.sol, line 621:     
                    totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];
                    
    File contracts/syndicate/Syndicate.sol, line 658:     totalClaimed += unclaimedUserShare;

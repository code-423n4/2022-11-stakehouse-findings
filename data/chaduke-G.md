G1. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L673
function ``_initStakingFundsVault()`` is simple and only called once, it saves gas to inline this code into its caller. 

G2. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L674
function ``_initSavETHVault()`` is simple and only called once, it saves gas to inline this code into its caller. 

G3: 
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L102
Duplicate assignment as Line  85.

G4: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L177
Not necessary since L176 would have reverted. 

G5: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L85
Change it to
```
 accumulatedETHPerLPShare =  accumulatedETHPerLPShare + (unprocessed * PRECISION) / _numOfShares;

```

G6. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L62-L64
The definition of ``totalShares`` should be defined in the parent contract ``SyndicateRewardsProcessor``. In this way, ``_updateAccumulatedETHPerLP(totalShares)`` does not need any argument and function ``updateAccumulatedETHPerLP()`` can be eliminated. This will save gas due to less passing of a state variable as argument and one less level of call.

G7: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L685
This function only checks msg.sender, so it cannot be zero. Eliminate this line. 

G8: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/StakingFundsVault.sol#L355
change the argument to calldata to save gas ``bytes[] calldata _blsPubKeys``

G9. 
Cache ``getAccountManager()`` as a state variable in ``StakingFundsVault.sol`` to save gas. It appears six times in the file.

G10. Cache ``liquidStakingNetworkManager.syndicate()`` in a state variable - it appears six time sin the file ``StakingFundsVault.sol``.

G10. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L232
cache ``getSlotRegistry()`` as a state variable in ``Syndicate.sol``, it appears 12 times in the file.

G11. cache ``getStakeHouseUniverse()`` as it appears six times in ``Syndicate.sol``.

G12, ``https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L948
Inline ``_updateDAORevenueCommission()`` to ``updateDAORevenueCommission()`` to save gas.

G13. cache ``getTransactionRouter()`` as it occurs six times in ``LiquidStakingManager.sol``.

G14. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L289
cache smartWalletRepresentative[smartWallet] to a memory variable to save gas.

G15 do no compare a bool to true or false 
for example
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L688
```
require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

```
should be
```
require(isNodeRunnerWhitelisted[_nodeRunner],  "Invalid node runner");
require(!isNodeRunnerBanned(msg.sender), "Node runner is banned from LSD network");

```

G16. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L301-L333
This block calculates the ``unclaimedUserShare`` for each ``_blsPubKey`` and then sends it to the recipient respectively. It would be gas saving to sum up all the  ``unclaimedUserShare`` for all ``_blsPubKey`` and then send the total ``unclaimedUserShare`` to the recipient in one call. 

G17. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L625
Function ``stakingFundsVault.updateDerivativesMinted()`` is called within a for loop so that the additional derivative minted can be updated for each knot. To save gas, one can calcualte to the ``totalNewDerivatives`` first, and then call ``stakingFundsVault.updateDerivativesMinted(totalNewDerivatives`)``. Of course, this will need a slight revision to the impelementation of ``stakingFundsVault.updateDerivativesMinted(`` as follows:

```
function updateDerivativesMinted(totalNewDerivatives) external onlyManager {
        totalShares = totalShares + totalNewDerivatives;
    }
```

G18: https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L770
change it to
``
stakedKnotsOfSmartWallet[smartWallet] =  stakedKnotsOfSmartWallet[smartWallet] + 1;
``
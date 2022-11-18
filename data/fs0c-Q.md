## [L-01] array out-of-Bounds

The length of `_ETHTransactionAmounts` is not compared to the length of other argument length which may lead to array OOB (this should just revert the transaction, hence the low-severity).

`contracts/liquid-staking/GiantMevAndFeesPool.sol`

```solidity
function batchDepositETHForStaking(
        address[] calldata _stakingFundsVault,
        uint256[] calldata _ETHTransactionAmounts, // length not checked for this [HERE]
        bytes[][] calldata _blsPublicKeyOfKnots,
        uint256[][] calldata _amounts
    ) external {
        uint256 numOfVaults = _stakingFundsVault.length;
        require(numOfVaults > 0, "Zero vaults");
        require(numOfVaults == _blsPublicKeyOfKnots.length, "Inconsistent lengths");
        require(numOfVaults == _amounts.length, "Inconsistent lengths");
        for (uint256 i; i < numOfVaults; ++i) {
            // As ETH is being deployed to a staking funds vault, it is no longer idle
            idleETH -= _ETHTransactionAmounts[i];

            StakingFundsVault sfv = StakingFundsVault(payable(_stakingFundsVault[i]));
            require(
                liquidStakingDerivativeFactory.isLiquidStakingManager(address(sfv.liquidStakingNetworkManager())),
                "Invalid liquid staking manager"
            );

            sfv.batchDepositETHForStaking{ value: _ETHTransactionAmounts[i] }(
                _blsPublicKeyOfKnots[i],
                _amounts[i]
            );
        }
    }
```
## Depositors cannot rotate LP Tokens

Depositors have an option to rotate their vault LP Tokens where the node runner did not stake the funds. 

If all funds needed for stake were collected and stake didn't happen, it is probable that at least one depositor will have issues rotating their LP Token.

Here are some scenarios that cause the rotation to fail:
1. Depositor is a single depositor of the full staking amount (24 ETH for savETHVault).
In such case the rotate function will revert. Example of the following `require` statement in a `savETHVault` scenario:

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/ETHPoolLPFactory.sol#L83
```
require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");
```
In order for a `newLPToken` to exist, it must have a minimum of `0.001` ETH (`MIN_STAKING_AMOUNT`) of `totalSupply`. 
Therefore a depositors trying to rotate his 24 ETH will fail because: `24 ether + 0.001 ether > 24 ether`  

2. In case there is no alternative LP Token that can hold the depositors amount the funds would freeze until a new BLS key is staked or an LP Token of other BLS was burned.

## ETH not updated in internal accounting of giant pools

Giant pools have a feature of bringing ETH back to the giant pools from the vaults.
The received ETH in not updated in internal accounting and therefore cannot be used for withdrawing funds which use the `idleETH` to determine how much ETH is idle in the giant pool

`bringUnusedETHBackIntoGiantPool`:
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L137
```
    function bringUnusedETHBackIntoGiantPool(
        address[] calldata _savETHVaults,
        LPToken[][] calldata _lpTokens,
        uint256[][] calldata _amounts
    ) external {
        uint256 numOfVaults = _savETHVaults.length;
        require(numOfVaults > 0, "Empty arrays");
        require(numOfVaults == _lpTokens.length, "Inconsistent arrays");
        require(numOfVaults == _amounts.length, "Inconsistent arrays");
        for (uint256 i; i < numOfVaults; ++i) {
            SavETHVault vault = SavETHVault(_savETHVaults[i]);
            for (uint256 j; j < _lpTokens[i].length; ++j) {
                require(
                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,
                    "ETH is either staked or derivatives minted"
                );
            }

            vault.burnLPTokens(_lpTokens[i], _amounts[i]);
        }
    }
```

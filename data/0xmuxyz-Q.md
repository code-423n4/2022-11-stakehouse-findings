### Report title
- Should use `custom error` statement instead of using `revert("<Error message written by string type>")` statement


### Code Snippet that has not been optimized yet
- Below are lines that `revert("<Error message written by string type>")` statement is still used and it has not been optimized yet:
  - https://github.com/masaun/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L734


### Description
- `Custom error` statement that consists of both `error()` statement and `revert()` statement has been able to use since solidity version 0.8.4
   - Using `custom error` statement have been introduced as a more `gas-efficient` way than using `revert("<Error message written by string type>")` statement.
       https://blog.soliditylang.org/2021/04/21/custom-errors/


### Recommendation
- Recommend to replace `revert("<Error message written by string type>")` statement with using `custom error` statement that consists of both `error()` statement and `revert()` statement to save gas.
  (Reference：https://blog.soliditylang.org/2021/04/21/custom-errors/ )

For example,
- In case of unoptimized-line ( https://github.com/masaun/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol#L734 ), this should be replaced with `custom error` statement like below:
```solidity
contract LiquidStakingManager is ILiquidStakingManager, Initializable, ReentrancyGuard, StakehouseAPI {

    //@dev - Define a custom error
    error UnexpectedState();

    〜〜Omission〜〜

    function _authorizeRepresentative(
        address _smartWallet, 
        address _eoaRepresentative, 
        bool _isEnabled
    ) internal {
        if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) {

            〜〜Omission〜〜

            emit RepresentativeAppointed(_smartWallet, _eoaRepresentative);
        } else {
            //@dev - Apply custom error
            revert UnexpectedState();
        }
    }
```
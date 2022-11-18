[[1]] GiantPoolBase - _assertUserHasEnoughGiantLPToClaimVaultLP - lastInteractedTimestamp check for user address happens in all iteration of batchs.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L66-L97
```
    /// @notice Allow a user to chose to withdraw vault LP tokens by burning their giant LP tokens. 1 Giant LP == 1 vault LP
    /// @param _lpTokens List of LP tokens being owned and being withdrawn from the giant pool
    /// @param _amounts List of amounts of giant LP being burnt in exchange for vault LP
    function withdrawLPTokens(LPToken[] calldata _lpTokens, uint256[] calldata _amounts) external {
        uint256 amountOfTokens = _lpTokens.length;
        require(amountOfTokens > 0, "Empty arrays");
        require(amountOfTokens == _amounts.length, "Inconsistent array lengths");

        _onWithdraw(_lpTokens);

        for (uint256 i; i < amountOfTokens; ++i) {
            LPToken token = _lpTokens[i];
            uint256 amount = _amounts[i];

            _assertUserHasEnoughGiantLPToClaimVaultLP(token, amount);

            // Burn giant LP from user before sending them an LP token from this pool
            lpTokenETH.burn(msg.sender, amount);

            // Giant LP tokens in this pool are 1:1 exchangeable with external savETH vault LP
            token.transfer(msg.sender, amount);

            emit LPSwappedForVaultLP(address(token), msg.sender, amount);
        }
    }

    /// @dev Check the msg.sender has enough giant LP to burn and that the pool has enough savETH vault LP
    function _assertUserHasEnoughGiantLPToClaimVaultLP(LPToken _token, uint256 _amount) internal view {
        require(_amount >= MIN_STAKING_AMOUNT, "Invalid amount");
        require(_token.balanceOf(address(this)) >= _amount, "Pool does not own specified LP");
        require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");   
    }
```
line: `require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");` can be checked one time in the function `withdrawLPTokens()` but right now it has been check in every iteration of loop.
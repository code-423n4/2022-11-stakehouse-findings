L-01:
StakingFundsVault#beforeTokenTransfer()/afterTokenTransfer() when #burn() , then to equal address(0),don't need to call _distributeETHRewardsToUserForToken()

```
    function beforeTokenTransfer(address _from, address _to, uint256) external override {
        address syndicate = liquidStakingNetworkManager.syndicate();
        if (syndicate != address(0)) {
...

                if (_from != address(0)) {
                    _distributeETHRewardsToUserForToken(_from, address(token), token.balanceOf(_from), _from);
                }
+               if (to != address(0)) {
                    _distributeETHRewardsToUserForToken(_to, address(token), token.balanceOf(_to), _to);
+               }

...

    function afterTokenTransfer(address, address _to, uint256) external override {
        if (LiquidStakingManager(payable(liquidStakingNetworkManager)).syndicate() != address(0)) {
            LPToken token = LPToken(msg.sender);
            require(KnotAssociatedWithLPToken[token].length > 0, "Invalid token");

+       if (to != address(0)) {
                claimed[_to][address(token)] = (token.balanceOf(_to) * accumulatedETHPerLPShare) / PRECISION;
+       }            



```


L-02:
GiantSavETHVaultPool#withdrawDETH() don't need check "lpTokenETH.balanceOf(msg.sender) >= amount" ,burn will check, suggest remove
```
    function withdrawDETH(
        address[] calldata _savETHVaults,
        LPToken[][] calldata _lpTokens,
        uint256[][] calldata _amounts
    ) external {

...

-               require(lpTokenETH.balanceOf(msg.sender) >= amount, "User does not own enough LP");

                // Burn giant LP from user before sending them dETH
                lpTokenETH.burn(msg.sender, amount);    //***@audit burn will check >=amount***/
```


L-03:
SavETHVault#burnLPToken() no check after burn , balance need >= MIN_STAKING_AMOUNT
```
    function burnLPToken(LPToken _lpToken, uint256 _amount) public nonReentrant returns (uint256) {
        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
        require(_amount <= _lpToken.balanceOf(msg.sender), "Not enough balance");
+       require(_lpToken.balanceOf(msg.sender)-_amount ==0 || _lpToken.balanceOf(msg.sender)-_amount>=MIN_STAKING_AMOUNT,"amount not reached");
```


L-04:
ETHPoolLPFactory#rotateLPTokens() no check after rotate , _oldLPToken.balanceOf() need >=MIN_STAKING_AMOUNT

```
    function rotateLPTokens(LPToken _oldLPToken, LPToken _newLPToken, uint256 _amount) public {
        require(address(_oldLPToken) != address(0), "Zero address");
        require(address(_newLPToken) != address(0), "Zero address");
        require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
        require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
        require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");
+       require(_oldLPToken.balanceOf(msg.sender)-_amount ==0 || _oldLPToken.balanceOf(msg.sender)-_amount>=MIN_STAKING_AMOUNT,"amount not reached");
```
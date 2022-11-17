Unnecessary check for `_amount` that is not used. https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L34

A `require` statement is used to make sure that `msg.value` is equal to the `_amount`, but this `_amount` value isn't actually used later on. Thus, the function parameter can be removed along with the `require` statement to save users some gas

```solidity
    function depositETH(uint256 _amount) public payable {
        require(msg.value >= MIN_STAKING_AMOUNT, "Minimum not supplied");
        require(msg.value == _amount, "Value equal to amount");

        // The ETH capital has not yet been deployed to a liquid staking network
        idleETH += msg.value;

        // Mint giant LP at ratio of 1:1
        lpTokenETH.mint(msg.sender, msg.value);

        // If anything extra needs to be done
        _onDepositETH();

        emit ETHDeposited(msg.sender, msg.value);
    }
```
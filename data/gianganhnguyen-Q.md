
# 1. [L-01] Boolean return value for `transfer` is not checked

There are 3 instances of this issue:

    File contracts/liquid-staking/GiantMevAndFeesPool.sol, line 184:     _lpTokens[i].transfer(address(this), _lpTokens[i].balanceOf(address(this)));
    // I suggest code replaced:
                        bool transferResult =  _lpTokens[i].transfer(address(this), _lpTokens[i].balanceOf(address(this)));
                        if (!transferResult) revert TransferFailed();

    File contracts/liquid-staking/GiantPoolBase.sol, line 86:    token.transfer(msg.sender, amount);
    // I suggest code replaced:
                        bool transferResult =  token.transfer(msg.sender, amount);
                        if (!transferResult) revert TransferFailed();

    File contracts/liquid-staking/GiantSavETHVaultPool.sol, line 108:     getDETH().transfer(msg.sender, dETHReceivedFromAllSavETHVaults);
    // I suggest code replaced:
                        bool transferResult =  getDETH().transfer(msg.sender, dETHReceivedFromAllSavETHVaults);
                        if (!transferResult) revert TransferFailed();

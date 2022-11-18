# 1. _setClaimedToMax is called repeatedly when depositETH to GiantMevAndFeesPool

code lines: 
- https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol#L42-L45
- https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol#L196-L198

`GiantMevAndFeesPool.depositETH` will call `lpTokenETH.mint(msg.sender, msg.value);` before `GiantMevAndFeesPool._onDepositETH -> GiantMevAndFeesPool._setClaimedToMax` function. When minting GiantLP token, `GiantMevAndFeesPool.afterTokenTransfer -> GiantMevAndFeesPool._setClaimedToMax` function will be called.

So `GiantMevAndFeesPool._onDepositETH` function is out of necessity.

# 2. mapping _isTransferApproved should be delete after transferOwnership

code lines:
- https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L94-L111

deleting `_isTransferApproved` after transferOwnership not only can refund storage gas fees but also ensures secure approvement after each owner transfer.


# 3. sETHUserClaimForKnot caculation

code lines:
- https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L652-L665

```
sETHUserClaimForKnot[_blsPubKey][msg.sender] =
                (accumulatedETHPerShare * sETHStakedBalanceForKnot[_blsPubKey][msg.sender]) / PRECISION;
```
can be modified to 
```
sETHUserClaimForKnot[_blsPubKey][msg.sender] += unclaimedUserShare;
```
because they are equal according to the operation in the function `calculateUnclaimedFreeFloatingETHShare`.

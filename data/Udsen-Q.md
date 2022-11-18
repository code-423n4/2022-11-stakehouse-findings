## 1. Target address is not checked for address(0). if address(0) is passed as the target the call function will return true. But the transaction will fail.

Should check for the non-zero target address. 
**`require(target != address(0), "Zero address");`**

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWallet.sol#L78

## 2. revert error message is given as ZeroAddress(). But it is checking for `_recipient == address(this)`. 
It is advisable to create a new error message for this condition as `ThisAddress()` in SyndicateErrors.sol and import it into Syndicate.sol.

*There are 2 instances of this issue:*

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#643
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#296
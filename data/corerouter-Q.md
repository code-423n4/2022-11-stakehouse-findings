1. https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWallet.sol#L78

It is suggested to parse the message to give details on why the call is failed. So it will better to change the codes at 
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/smart-wallet/OwnableSmartWallet.sol#L79

from:
require(result, "Failed to execute");

to:

if (!result) {
            // Look for revert reason and parse it if present
            if (message.length > 0) {
                // The easiest way to parse the revert reason is using memory via assembly
                assembly {
                    let message_size := mload(message)
                    revert(add(32, message), message_size)
                }
            } else {
                revert("Failed to execute");
            }
    }
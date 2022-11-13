## TABLE OF CONTENTS

- [G-01] Splitting require() statements that use && saves gas
- [G-02] Boolean comparison , use !x instead of x == false

### [G-01] Splitting require() statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 3 GAS per &&

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L357

### [G-02] Boolean comparison , use !x instead of x == false

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L611

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L612
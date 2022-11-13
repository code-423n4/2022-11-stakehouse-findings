## TABLE OF CONTENTS

- [N-01] Non-library/interface files should use fixed compiler versions, not floating ones.
- [N-02] Avoid the use of sensitive terms in favour of neutral ones
- [L-01] Open Todos
- [L-02] _safemint() should be used rather than _mint() whereever possible

### [N-01] Non-library/interface files should use fixed compiler versions, not floating ones.

For example: 

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L1

### [N-02] Avoid the use of sensitive terms in favour of neutral ones

Use allowlist rather than whitelist

For example: 

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L267

### [L-01] Open Todos

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L195

### [L-02] _safemint() should be used rather than _mint() whereever possible

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantLP.sol#L29

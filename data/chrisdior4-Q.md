# [N-01] Compile all project with the same solidity version

 

Solidity pragma versioning should be exactly same in all contracts. Currently some contracts use ^0.8.13 but some are fixed to 0.8.13

 

# [N-02] Its best practice for the events to be declared in the smart contract interface

 

Examples: `Syndicate.sol`, `LiquidStakingManager.sol`

 
# [N-03] Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

event ETHWithdrawnByDepositor(address depositor, uint256 amount);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/ETHPoolLPFactory.sol#L16

event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/ETHPoolLPFactory.sol#L19

event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/ETHPoolLPFactory.sol#L22

event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/ETHPoolLPFactory.sol#L25

event ETHDeposited(address sender, uint256 amount);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/StakingFundsVault.sol#L25

event ETHWithdrawn(address receiver, address admin, uint256 amount);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/StakingFundsVault.sol#L28

event ERC20Recovered(address admin, address recipient, uint256 amount);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/StakingFundsVault.sol#L31

event WETHUnwrapped(address admin, uint256 amount);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/StakingFundsVault.sol#L34

 

#  [L-02] Use the latest version of OpenZeppelin

 

To prevent any issues in the future (e.g. using solely hardhat to compile and deploy the contracts), upgrade the used OZ packages within the package.json to the latest versions. Currently the project is using 4.5.0 flowtable.

 

Upgrade it to stable 4.8.0 (which is the newest)

 

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/package.json#L15

 

 

# [L-03] Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

 

File: LiquidStakingManager.sol

 

  uint256 public MODULO = 100_00000;

  

  Should be:

 

  uint256 public MODULO = 1e7

 

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/LiquidStakingManager.sol#L158

 
# [L-02] Require should be used instead of Assert

Solidity documents mention that properly functioning code should never reach a failing assert statement
and if this happens there is a bug in the contract which should be fixed.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require

assert(syndicateFactoryMock.slot() == slot);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/testing/liquid-staking/MockLSDNFactory.sol#L73

Replace assert by require.

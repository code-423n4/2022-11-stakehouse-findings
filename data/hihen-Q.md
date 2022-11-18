* Lack of validity checking for important parameters
  - GiantLP.sol - constructor(): `_pool` should be none-zero.
  - LPToken.sol - init(): `_deployer` should be none-zero.
  - GiantSavETHVaultPool.sol - constructor(): `_factory` should be none-zero.
  - GiantMevAndFeesPool.sol - constructor(): `_factory` should be none-zero.
  - Syndicate.sol - `_initialize()`: `_contractOwner`, should be none-zero.
* Incorrect variable naming
  - GiantLP.sol: `_recipient` in `function burn(address _recipient, uint256 _amount)` is incorrect, should be renamed to `_account`
  - LPToken.sol: `_recipient` in `function burn(address _recipient, uint256 _amount)` is incorrect, should be renamed to `_account`
* Redundant parameters can be removed
  - GiantPoolBase.sol - `depositETH()`: `_amount` is Redundant, could be removed and use `msg.value` only.
    ```
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
* Constants should be defined rather than using magic numbers. It is bad practice to use numbers directly in code without explanation.
  - GiantSavETHVaultPool.sol: `0.5 ether`
  - GiantMevAndFeesPool.sol: `0.5 ether`
  - ETHPoolLPFactory.sol: `24 ether`, `30 minutes`
  - LiquidStakingManager.sol: `24 ether`, `4 ether`, 
  - SavETHVault.sol: `24 ether`, `30 minutes`
  - StakingFundsVault.sol: `4 ether`, `30 minutes`
  - Syndicate.sol: `4 ether`, 
  - GiantPoolBase.sol: `1 days`

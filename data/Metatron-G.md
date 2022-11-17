### Upgrade pragma to 0.8.16 to save gas

Across the whole solution, the declared pragma is 0.8.13
Upgrading to 0.8.16 has many benefits, cheaper on gas is one of them.
The following information is regarding 0.8.15 over 0.8.14. Assume that 0.8.16 over 0.8.13 the save is higher!
```
According to the release note of 0.8.15: https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/
The benchmark shows saving of 2.5-10% Bytecode size,
Saving 2-8% Deployment gas,
And saving up to 6.2% Runtime gas.
```

---------------------------------------------------------------------------

### Use immutable for state variables that are only set in the constructor - to save gas

Save around 20,000 gas per uint256 variable (use 100+3 gas for immutable variable instead of 20000 for regular public)

In the following locations the variable is set only at the constructor and only being read afterward,
I recommend changing all of the 3 to immutable to save gas:

```
contracts/liquid-staking/GiantLP.sol:
  10      /// @notice Address of giant pool that deployed the giant LP token
  11:     address public pool;  // @audit can be immutable
  12  
  13      /// @notice Optional address of contract that will process transfers of giant LP
  14:     ITransferHookProcessor public transferHookProcessor; // @audit can be immutable
  15  

contracts/liquid-staking/OptionalHouseGatekeeper.sol:
  11      /// @notice Address of the core registry for the associated liquid staking network
  12:     ILiquidStakingManager public liquidStakingManager;  //@audit can be immutable
  13  
```


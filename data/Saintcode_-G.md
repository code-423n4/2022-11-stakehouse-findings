## USE CALLDATA INSTEAD OF MEMORY

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

When arguments are read-only on external functions, the data location should be `calldata`

Instances:
-LiquidStakingManager.sol line 879
-StakingFundsVault.sol lines 355, 360
-OwnableSmartWallet.sol lines 41, 54, 69
-Syndicate.sol lines 132, 133, 337, 343, 359, 472, 473


## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

Reading array length at each iteration of the loop consumes more gas than necessary.
In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Consider storing the array’s length in a variable before the for-loop, and use this new variable instead

Instances:
-GiantMevAndFeesPool.sol lines 90, 183
-GiantSavETHVaultPool.sol lines 82, 148
-StakingFundVault.sol lines 203, 266, 276, 286
-Syndicate.sol lines 211, 259, 301, 346, 585, 598, 648

## USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another memory array/struct

Instances:
-ETHPoolFactory.sol line 85, 86
-SaveETHVault.sol line 131, 229
-StakingFundsVault.sol line 179, 295, 319


## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

Instances:
-LiquidStakingManager.sol lines 782, 839
-StakingFundVault.sol lines 58, 
-SyndicateRewardsProcessor.sol lines 65, 85
-Syndicate.sol lines 185, 190, 225, 226, 227, 317, 511, 521, 558, 658


## USE MULTIPLE `REQUIRE()` STATMENTS INSTEAD OF `REQUIRE(EXPRESSION && EXPRESSION && ...)`

Instance:
-LiquidStakingManager.sol line 357
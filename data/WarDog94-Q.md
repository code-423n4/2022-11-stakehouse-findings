Moved sender check for clarity and to be aligned with the rest of the code in other contracts
```diff
diff --git a/contracts/liquid-staking/GiantLP.sol b/contracts/liquid-staking/GiantLP.sol
index e6026f9..a1abcba 100644
--- a/contracts/liquid-staking/GiantLP.sol
+++ b/contracts/liquid-staking/GiantLP.sol
@@ -16,6 +16,11 @@ contract GiantLP is ERC20 {
     /// @notice Last interacted timestamp for a given address
     mapping(address => uint256) public lastInteractedTimestamp;
 
+    modifier onlyPool {
+        require(msg.sender == pool, "Only pool");
+        _;
+    }
+
     constructor(
         address _pool,
         address _transferHookProcessor,
@@ -26,13 +31,11 @@ contract GiantLP is ERC20 {
         transferHookProcessor = ITransferHookProcessor(_transferHookProcessor);
     }
 
-    function mint(address _recipient, uint256 _amount) external {
-        require(msg.sender == pool, "Only pool");
+    function mint(address _recipient, uint256 _amount) external onlyPool {        
         _mint(_recipient, _amount);
     }
 
-    function burn(address _recipient, uint256 _amount) external {
-        require(msg.sender == pool, "Only pool");
+    function burn(address _recipient, uint256 _amount) external onlyPool {
         _burn(_recipient, _amount);
     }
```

Added immutable modifier to express intent visibly

```diff 
diff --git a/contracts/liquid-staking/LSDNFactory.sol b/contracts/liquid-staking/LSDNFactory.sol
index 7007e1a..b605712 100644
--- a/contracts/liquid-staking/LSDNFactory.sol
+++ b/contracts/liquid-staking/LSDNFactory.sol
@@ -12,28 +12,28 @@ contract LSDNFactory {
     event LSDNDeployed(address indexed LiquidStakingManager);
 
     /// @notice Address of the liquid staking manager implementation that is cloned on each deployment
-    address public liquidStakingManagerImplementation;
+    address public immutable liquidStakingManagerImplementation;
 
     /// @notice Address of the factory that will deploy a syndicate for the network after the first knot is created
     address public syndicateFactory;
 
     /// @notice Address of the factory for deploying LP tokens in exchange for ETH supplied to stake a KNOT
-    address public lpTokenFactory;
+    address public immutable lpTokenFactory;
 
     /// @notice Address of the factory for deploying smart wallets used by node runners during staking
-    address public smartWalletFactory;
+    address public immutable smartWalletFactory;
 
     /// @notice Address of brand NFT
-    address public brand;
+    address public immutable brand;
 
     /// @notice Address of the contract that can deploy new instances of SavETHVault
-    address public savETHVaultDeployer;
+    address public immutable savETHVaultDeployer;
 
     /// @notice Address of the contract that can deploy new instances of StakingFundsVault
-    address public stakingFundsVaultDeployer;
+    address public immutable stakingFundsVaultDeployer;
 
     /// @notice Address of the contract that can deploy new instances of optional gatekeepers for controlling which knots can join the LSDN house
-    address public optionalGatekeeperDeployer;
+    address public immutable optionalGatekeeperDeployer;
 
     /// @notice Establishes whether a given liquid staking manager address was deployed by this factory
     mapping(address => bool) public isLiquidStakingManager;
diff --git a/contracts/liquid-staking/OptionalHouseGatekeeper.sol b/contracts/liquid-staking/OptionalHouseGatekeeper.sol
index fabedeb..2ad8d5d 100644
--- a/contracts/liquid-staking/OptionalHouseGatekeeper.sol
+++ b/contracts/liquid-staking/OptionalHouseGatekeeper.sol
@@ -9,7 +9,7 @@ import { ILiquidStakingManager } from "../interfaces/ILiquidStakingManager.sol";
 contract OptionalHouseGatekeeper is IGateKeeper {
 
     /// @notice Address of the core registry for the associated liquid staking network
-    ILiquidStakingManager public liquidStakingManager;
+    ILiquidStakingManager public immutable liquidStakingManager;
 
     constructor(address _manager) {
         liquidStakingManager = ILiquidStakingManager(_manager);
diff --git a/contracts/liquid-staking/SavETHVaultDeployer.sol b/contracts/liquid-staking/SavETHVaultDeployer.sol
index 59e897e..b853bb9 100644
--- a/contracts/liquid-staking/SavETHVaultDeployer.sol
+++ b/contracts/liquid-staking/SavETHVaultDeployer.sol
@@ -10,7 +10,7 @@ contract SavETHVaultDeployer {
 
     event NewVaultDeployed(address indexed instance);
 
-    address implementation;
+    address immutable implementation;
     constructor() {
         implementation = address(new SavETHVault());
     }
diff --git a/contracts/liquid-staking/StakingFundsVaultDeployer.sol b/contracts/liquid-staking/StakingFundsVaultDeployer.sol
index 628e1bd..2d20487 100644
--- a/contracts/liquid-staking/StakingFundsVaultDeployer.sol
+++ b/contracts/liquid-staking/StakingFundsVaultDeployer.sol
@@ -10,7 +10,7 @@ contract StakingFundsVaultDeployer {
 
     event NewVaultDeployed(address indexed instance);
 
-    address implementation;
+    address immutable implementation;
     constructor() {
         implementation = address(new StakingFundsVault());
     }
```
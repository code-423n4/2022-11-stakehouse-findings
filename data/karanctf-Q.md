

## The `immutable` and `constant` variables should be written in uppercase.
**Details**: According to the [Solidity](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[ ](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[Style](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[ ](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[Guide](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[ ](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)constants should be named with all capital letters with underscores separating words. Immutables can be treated similar to constants because they are replaced by their assigned values during contract construction.
```solidity
contracts/smart-wallet/OwnableSmartWalletFactory.sol:14:    address public immutable masterWallet;
```

## Use scientific notation (e.g. `10e18`)
```solidity
contracts/liquid-staking/LiquidStakingManager.sol:158:    uint256 public MODULO = 100_00000;
```

## Use of single quotes in require
```solidity
contracts/smart-wallet/OwnableSmartWalletFactory.sol:37:        require(owner != address(0), 'Wallet cannot be address 0');
```
## No `indexed` event parameters
**Summary**: Events with no indexed parameters. This could make it harder and inefficient for off-chain tools to analyse them.
**Details**: Indexed parameters (“topics”) are searchable event parameters. They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
```solidity
contracts/syndicate/Syndicate.sol:39:    event ContractDeployed();
contracts/syndicate/Syndicate.sol:42:    event UpdateAccruedETH(uint256 unprocessed);
contracts/syndicate/Syndicate.sol:45:    event CollateralizedSLOTReCalibrated(bytes BLSPubKey);
contracts/syndicate/Syndicate.sol:48:    event KNOTRegistered(bytes BLSPubKey);
contracts/syndicate/Syndicate.sol:51:    event KnotDeRegistered(bytes BLSPubKey);
contracts/syndicate/Syndicate.sol:57:    event Staked(bytes BLSPubKey, uint256 amount);
contracts/syndicate/Syndicate.sol:60:    event UnStaked(bytes BLSPubKey, uint256 amount);
contracts/liquid-staking/SavETHVault.sol:19:    event DETHRedeemed(address depositor, uint256 amount);
contracts/liquid-staking/SavETHVault.sol:22:    event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);
contracts/liquid-staking/SavETHVault.sol:121:    event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);
contracts/liquid-staking/StakingFundsVault.sol:25:    event ETHDeposited(address sender, uint256 amount);
contracts/liquid-staking/StakingFundsVault.sol:28:    event ETHWithdrawn(address receiver, address admin, uint256 amount);
contracts/liquid-staking/StakingFundsVault.sol:31:    event ERC20Recovered(address admin, address recipient, uint256 amount);
contracts/liquid-staking/StakingFundsVault.sol:34:    event WETHUnwrapped(address admin, uint256 amount);
contracts/liquid-staking/LiquidStakingManager.sol:57:    event StakehouseJoined(bytes blsPubKey);
contracts/liquid-staking/LiquidStakingManager.sol:69:    event NetworkTickerUpdated(string newTicker);
contracts/liquid-staking/LiquidStakingManager.sol:84:    event DAOCommissionUpdated(uint256 old, uint256 newCommission);
contracts/liquid-staking/ETHPoolLPFactory.sol:16:    event ETHWithdrawnByDepositor(address depositor, uint256 amount);
contracts/liquid-staking/ETHPoolLPFactory.sol:19:    event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
contracts/liquid-staking/ETHPoolLPFactory.sol:22:    event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);
contracts/liquid-staking/ETHPoolLPFactory.sol:25:    event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);
contracts/liquid-staking/SyndicateRewardsProcessor.sol:9:    event ETHReceived(uint256 amount);
```


## Open TODOs
**Summary**:In code comments should be removed as they imply that the code is incomplete or buggy, or that the implementation may not have been entirely followed.
```solidity
contracts/syndicate/Syndicate.sol:195:            // todo - check else case for any ETH lost
```

## Use of `Block.timestamp`
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
```solidity
contracts/liquid-staking/GiantPoolBase.sol:96:        require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");
contracts/liquid-staking/SavETHVault.sol:141:        bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;
contracts/liquid-staking/LPToken.sol:67:        lastInteractedTimestamp[_from] = block.timestamp;
contracts/liquid-staking/LPToken.sol:68:        lastInteractedTimestamp[_to] = block.timestamp;
contracts/liquid-staking/StakingFundsVault.sol:184:        require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");
contracts/liquid-staking/StakingFundsVault.sol:230:            require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");
contracts/liquid-staking/GiantLP.sol:44:        lastInteractedTimestamp[_from] = block.timestamp;
contracts/liquid-staking/GiantLP.sol:45:        lastInteractedTimestamp[_to] = block.timestamp;
contracts/liquid-staking/ETHPoolLPFactory.sol:82:        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
```


## `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
```solidity
contracts/liquid-staking/ETHPoolLPFactory.sol-131-            
contracts/liquid-staking/ETHPoolLPFactory.sol-132-            // mint new LP tokens for the new KNOT
contracts/liquid-staking/ETHPoolLPFactory.sol-133-            // add the KNOT in the mapping
contracts/liquid-staking/ETHPoolLPFactory.sol-134-            string memory tokenNumber = Strings.toString(numberOfLPTokensIssued);
contracts/liquid-staking/ETHPoolLPFactory.sol:135:            string memory tokenName = string(abi.encodePacked(baseLPTokenName, tokenNumber));
contracts/liquid-staking/ETHPoolLPFactory.sol:136:            string memory tokenSymbol = string(abi.encodePacked(baseLPTokenSymbol, tokenNumber));
contracts/liquid-staking/ETHPoolLPFactory.sol-137-
contracts/liquid-staking/ETHPoolLPFactory.sol-138-            // deploy new LP token and optionally enable transfer notifications
contracts/liquid-staking/ETHPoolLPFactory.sol-139-            LPToken newLPToken = _enableTransferHook ?
contracts/liquid-staking/ETHPoolLPFactory.sol-140-                             LPToken(lpTokenFactory.deployLPToken(address(this), address(this), tokenSymbol, tokenName)) :
```


## Use `allowlist/denylist` rather than `blacklist/whitelist`
```solidity
contracts/liquid-staking/LiquidStakingManager.sol:35:    /// @notice signalize change in status of whitelisting
contracts/liquid-staking/LiquidStakingManager.sol:36:    event WhitelistingStatusChanged(address indexed dao, bool updatedStatus);
contracts/liquid-staking/LiquidStakingManager.sol:38:    /// @notice signalize updated whitelist status of node runner
contracts/liquid-staking/LiquidStakingManager.sol:39:    event NodeRunnerWhitelistingStatusChanged(address indexed nodeRunner, bool updatedStatus);
contracts/liquid-staking/LiquidStakingManager.sol:119:    /// @notice whitelisting indicator. true for enables and false for disabled
contracts/liquid-staking/LiquidStakingManager.sol:120:    bool public enableWhitelisting;
contracts/liquid-staking/LiquidStakingManager.sol:122:    /// @notice mapping to store if a node runner is whitelisted
contracts/liquid-staking/LiquidStakingManager.sol:123:    mapping(address => bool) public isNodeRunnerWhitelisted;
contracts/liquid-staking/LiquidStakingManager.sol:265:    /// @notice function to change whether node runner whitelisting of node runners is required by the DAO
contracts/liquid-staking/LiquidStakingManager.sol:266:    /// @param _changeWhitelist boolean value. true to enable and false to disable
contracts/liquid-staking/LiquidStakingManager.sol:267:    function updateWhitelisting(bool _changeWhitelist) external onlyDAO returns (bool) {
contracts/liquid-staking/LiquidStakingManager.sol:268:        require(_changeWhitelist != enableWhitelisting, "Unnecessary update to same status");
contracts/liquid-staking/LiquidStakingManager.sol:269:        enableWhitelisting = _changeWhitelist;
contracts/liquid-staking/LiquidStakingManager.sol:270:        emit WhitelistingStatusChanged(msg.sender, enableWhitelisting);
contracts/liquid-staking/LiquidStakingManager.sol:272:        return enableWhitelisting;
contracts/liquid-staking/LiquidStakingManager.sol:275:    /// @notice function to enable/disable whitelisting of a noderunner
contracts/liquid-staking/LiquidStakingManager.sol:277:    /// @param isWhitelisted true if the node runner should be whitelisted. false otherwise.
contracts/liquid-staking/LiquidStakingManager.sol:278:    function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {
contracts/liquid-staking/LiquidStakingManager.sol:280:        require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
contracts/liquid-staking/LiquidStakingManager.sol:282:        isNodeRunnerWhitelisted[_nodeRunner] = isWhitelisted;
contracts/liquid-staking/LiquidStakingManager.sol:283:        emit NodeRunnerWhitelistingStatusChanged(_nodeRunner, isWhitelisted);
contracts/liquid-staking/LiquidStakingManager.sol:453:        // Ensure that the node runner does not whitelist multiple EOA representatives - they can only have 1 active at a time
contracts/liquid-staking/LiquidStakingManager.sol:681:    /// @dev function checks if a node runner is valid depending upon whitelisting status
contracts/liquid-staking/LiquidStakingManager.sol:687:        if(enableWhitelisting) {
contracts/liquid-staking/LiquidStakingManager.sol:688:            require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");
```

# QA
# Low
## Prevent div by 0
### Impact
On several locations in the code precautions are being taken to not divide by `0`, this should be done as a division by `0` would revert the code.

### Proof of Concept
Navigate to the following contracts,

- `numberOfRegisteredKnots` it is being checked one time to not be 0, but not on the other instances

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L407
                    + ((calculateETHForFreeFloatingOrCollateralizedHolders() - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L447
        return ((calculateETHForFreeFloatingOrCollateralizedHolders() - lastSeenETHPerCollateralizedSlotPerKnot) / numberOfRegisteredKnots);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L540
        uint256 collateralizedSLOTShareOfETHPerKnot = (collateralizedSLOTShareOfETH / numberOfRegisteredKnots);


https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L546
        return (_ethSinceLastUpdate * PRECISION) / (numberOfRegisteredKnots * 4 ether);

- not checked to not be 0
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L43
            uint256 newAccumulatedETH = accumulatedETHPerLPShare + ((unprocessed * PRECISION) / _numOfShares);


### Recommended Mitigation Steps
Recommend making sure division by `0` won’t occur by checking the variables beforehand and handling this edge case.


## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L20-L21
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L19
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L20
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L15
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/SyndicateFactory.sol#L17
### Mitigation
Check zero address before assigning or using it


## Emitted amount can be bigger than expected
### Impact 
There are ERC20 tokens with transfer at fees. For checking if the transferred amount is the same as expected, code already compares balanceOf before and balanceOf after transfer. People can get confused in cases where real value doesn't match, also applications like subgraphs that uses this value won't work as expected.

### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L233-L236
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L85-L88
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L275-L278
### Mitigation
Consider implementing a system like:
```
        uint256 balanceBefore = _token.balanceOf(address(this));
        _token.safeTransferFrom(_from, address(this), _amount);
        uint256 balanceAfter = _token.balanceOf(address(this));

        // check / control flow when (balanceAfter - balanceBefore != _amount);
```
### Recommendation
Consider comparing before and after balance to get the actual transferred amount.



## block.timestamp used as time proxy 
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
- `block.timestamp` for comparisons may not work as expected
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L96
        require(lpTokenETH.lastInteractedTimestamp(msg.sender) + 1 days < block.timestamp, "Too new");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L141
        bool isStaleLiquidity = _lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp;

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L184
        require(_lpToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Too new");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L230
            require(token.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Last transfer too recent");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L82
        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");

- `block.timestamp` as time
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L44
        lastInteractedTimestamp[_from] = block.timestamp;

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L45
        lastInteractedTimestamp[_to] = block.timestamp;

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L67
        lastInteractedTimestamp[_from] = block.timestamp;

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L68
        lastInteractedTimestamp[_to] = block.timestamp;


### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision


## Front run initializer
### Summary
The initialize function that initializes important contract state can be called by anyone.
### Details
The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.

In the best case for the victim, they notice it and have to redeploy their contract costing gas.

### Github Permalinks

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/smart-wallet/OwnableSmartWallet.sol#L28
    function initialize(address initialOwner)

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L129
    function initialize(

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L32
    function init(

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L45
    function init(address _liquidStakingManagerAddress, LPTokenFactory _lpTokenFactory) external virtual initializer {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L46
    function init(address _liquidStakingNetworkManager, LPTokenFactory _lpTokenFactory) external virtual initializer {

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L169
    function init(



### Mitigation
Use the constructor to initialize non-proxied contracts.

For initializing proxy contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify the transaction succeeded.


## Return value not being checked 
### Details
Return values not being checked may lead into unexpected behaviors with functions.

### Github Permalinks

- `withdrawETH` not being checked
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L743

- `withdrawETHForStaking` not being checked
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L739-L773

- `rawExecute` not being checked
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L326-L350

- `withdrawETHForKnot` not being checked
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L326-L350

- `approve` not being checked
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L816-L876

- `execute` not being checked
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L841-L881
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L202-L215
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L739-L773
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L776-L813
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L695-L736
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L816-L876
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L426-L492
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L382-L420


### Mitigation
Check values and `revert`/`emit` events if needed



# Informational
## Comparison with a a boolean
### Summary
There are a number of instances where a boolean variable/function is checked.
### Details
- This check can be further simplified from `variable == false` to `!variable`.
- This check can be further simplified from `variable == true` to `variable`.

### Github Permalink
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L612
        if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L436
        require(_isNodeRunnerValid(msg.sender) == true, "Unrecognised node runner");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L688
            require(isNodeRunnerWhitelisted[_nodeRunner] == true, "Invalid node runner");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L150
                    vault.isDETHReadyForWithdrawal(address(_lpTokens[i][j])) == false,

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L64
            require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L84
        require(liquidStakingManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L79
            require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is not part of LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L114
        require(liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key is banned or not a part of LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L205
                liquidStakingNetworkManager.isBLSPublicKeyBanned(_blsPubKeys[i]) == false,

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L611
        if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L291
        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L328
        require(isBLSPublicKeyBanned(_blsPublicKeyOfKnot) == false, "BLS public key has already withdrawn or not a part of LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L332
        require(isNodeRunnerBanned(nodeRunnerOfSmartWallet[associatedSmartWallet]) == false, "Node runner is banned from LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L393
            require(isBLSPublicKeyBanned(_blsPubKeys[i]) == false, "BLS public key is banned or not a part of LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L437
        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L469
            require(isBLSPublicKeyPartOfLSDNetwork(_blsPublicKey) == false, "BLS public key is banned or not a part of LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L541
            require(isBLSPublicKeyBanned(blsPubKey) == false, "BLS public key is banned or not a part of LSD network");

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L589
            require(isBLSPublicKeyBanned(_blsPublicKeyOfKnots[i]) == false, "BLS public key is banned or not a part of LSD network");


### Mitigation
Simplify boolean comparisons in order to improve readability and save gas



## Missing indexed event parameters
### Summary
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L19
    event DETHRedeemed(address depositor, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L22
    event ETHWithdrawnForStaking(address withdrawalAddress, address liquidStakingManager, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L121
    event CurrentStamp(uint256 stamp, uint256 last, bool isConditionTrue);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L25
    event ETHDeposited(address sender, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L28
    event ETHWithdrawn(address receiver, address admin, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L31
    event ERC20Recovered(address admin, address recipient, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L34
    event WETHUnwrapped(address admin, uint256 amount);


https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L42
    event UpdateAccruedETH(uint256 unprocessed);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L45
    event CollateralizedSLOTReCalibrated(bytes BLSPubKey);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L48
    event KNOTRegistered(bytes BLSPubKey);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L57
    event Staked(bytes BLSPubKey, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L60
    event UnStaked(bytes BLSPubKey, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L57
    event StakehouseJoined(bytes blsPubKey);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L69
    event NetworkTickerUpdated(string newTicker);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L84
    event DAOCommissionUpdated(uint256 old, uint256 newCommission);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L9
    event ETHReceived(uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L16
    event ETHWithdrawnByDepositor(address depositor, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L19
    event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L22
    event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L25
    event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

- No parameters
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L39
    event ContractDeployed();

### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.



## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity >=0.13. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/OptionalGatekeeperFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/OptionalHouseGatekeeper.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVaultDeployer.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVaultDeployer.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/smart-wallet/OwnableSmartWalletFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPTokenFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LSDNFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/smart-wallet/OwnableSmartWallet.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SyndicateRewardsProcessor.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol

### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting ^ 0.8.13 into 0.8.13



## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions
Also, state variables order affects to gas in the same way as ordering structs for saving storage slots

### Github Permalink
- events before state variables
newoption y lpburned
https://github.com/code-423n4/2022-11-stakehouse/blob/a0558ed7b12e1ace1fe5c07970c7fc07eb00eebd/contracts/liquid-staking/ETHPoolLPFactory.sol#L16-L25
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L12-L19
https://github.com/code-423n4/2022-11-stakehouse/blob/a0558ed7b12e1ace1fe5c07970c7fc07eb00eebd/contracts/liquid-staking/LiquidStakingManager.sol#L36-L87
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPTokenFactory.sol#L12
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LSDNFactory.sol#L12
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L19-L22
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVaultDeployer.sol#L11
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L25-L34
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L11
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L8-L13
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L39-L63

- modifier after functions and constructor
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L50-L53
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L49-L52



### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.16/style-guide.html




## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 

### Details 
Contracts has partial or full lack of comments

### Github Permalinks 
- Total or partial missing Natspec: `@param`, `@return`...
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/OptionalGatekeeperFactory.sol#L10-L17
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L14-L22
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVaultDeployer.sol#L17-L25
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVaultDeployer.sol#L18-L25
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/smart-wallet/OwnableSmartWalletFactory.sol#L1-L45
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPTokenFactory.sol#L17-L48
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L1-L48
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L1-L71
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/SyndicateFactory.sol#L1-L65
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LSDNFactory.sol#L41-L102
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L145-L205
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L197-L382
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L1-L99
https://github.com/code-423n4/2022-11-stakehouse/blob/a0558ed7b12e1ace1fe5c07970c7fc07eb00eebd/contracts/liquid-staking/LiquidStakingManager.sol#L1-L955
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L91-L97

0xReturnValue

 ### Mitigation
 - Add `@param` descriptors
 - Complete the cases where some `@param` is missing
 - Add `@return` descriptors



## Variable shadows another variable
###  Summary 
Name shadowing where two or more variables/functions share the same name could be confusing to developers and/or reviewers
###  Details 
Use of `_symbol` and `_name` that are variables in OZ ERC20
###  Github Permalinks 
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L22
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L23

###  Mitigation
- Replace `_name` variable into `name_`, `new_name` or a similar substitution
- Replace `_symbol` variable into `symbol_`, `new_symbol` or a similar substitution





## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 120 character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length
### Github Permalinks 


https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/OptionalHouseGatekeeper.sol#L8

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L40

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantLP.sol#L46

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L6

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L62

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LPToken.sol#L69

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L27

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L30

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L50

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L66

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L102

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LSDNFactory.sol#L35

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L23

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L62

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L65

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L111

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L114

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L7

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L55

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L57

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L64

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L66

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L83

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L84

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L86

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L98

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L111

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L132

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L140

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L158

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L165

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L217

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L222

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L227

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/SavETHVault.sol#L230

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L22

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L26

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L97

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L100

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L103

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L118

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantMevAndFeesPool.sol#L195

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L9

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L55

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L69

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L79

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L81

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L103

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L113

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L114

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L116

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L140

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L181

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L197

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L211

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L215

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L217

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L230

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L253

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L273

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L274

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L283

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L292

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L296

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L300

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L322

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L336

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L354

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/StakingFundsVault.sol#L371

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L35

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L63

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L71

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L89

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L92

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L95

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L116

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L119

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L127

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L128

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L153

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L166

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L173

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L175

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L185

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L189

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L201

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L202

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L216

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L228

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L311

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L335

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L341

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L356

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L359

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L372

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L381

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L390

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L398

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L407

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L411

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L416

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L447

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L490

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L503

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L505

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L506

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L510

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L511

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L514

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L531

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L532

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L545

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L550

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L573

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L575

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L596

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L620

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L661

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L12

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L13

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L14

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L66

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L222

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L224

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L280

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L287

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L305

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L323

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L328

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L331

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L332

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L335

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L356

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L393

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L396

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L453

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L455

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L469

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L472

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L501

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L518

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L519

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L523

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L546

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L573

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L574

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L589

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L593

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L632

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L633

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L785

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L823

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L858

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L921

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L933

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L7

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L72

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L82

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L92

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L97

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L110

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L122

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/ETHPoolLPFactory.sol#L140


### Mitigation
Reduce line length to less than 120 at least to improve maintainability and readability of the code 


## Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability
### Summary
Multiples of 10 can be declared as constants with scientific notation so it's easier to read them and less prone to miss/exceed a 0 of the expected value.

### Details
Values        `NUMBER_WITH_MANY_ZEROS` and `NUMBER_WITH_MANY_ZEROS_2`       can be used in scientific notation
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/LiquidStakingManager.sol#L158
    uint256 public MODULO = 100_00000;

### Mitigation
Replace hardcoded numbers with constants that represent the scientific corresponding notation



## State variables that do not change should be constant and written in UPPERCASE
### Summary
`constant` keyword helps with readability of the code and to make sure that they do not change. 

### Details
Code contains state variables that do not change and so they can be declared `constant`

### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/a0558ed7b12e1ace1fe5c07970c7fc07eb00eebd/contracts/liquid-staking/LiquidStakingManager.sol#L158


### Mitigation
Add constant and change `VariableName` to `VARIABLE_NAME`


## Max value can be used 
### Summary
Rather than using `2 ** 256 - 1`, type(uint256).max can be used

### Github Permalinks
`sETH.approve(syndicate, (2 ** 256) - 1);`
https://github.com/code-423n4/2022-11-stakehouse/blob/a0558ed7b12e1ace1fe5c07970c7fc07eb00eebd/contracts/liquid-staking/LiquidStakingManager.sol#L870

### Mitigation
Consider changing calculated value by max type value


## Naming convention of state variable non constant
### Summary
Only constants are suggested to use style `CONSTANTS_WITH_UNDERSCORES`, other variables are suggested to use `camelCase`
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/a0558ed7b12e1ace1fe5c07970c7fc07eb00eebd/contracts/liquid-staking/LiquidStakingManager.sol#L158

### Mitigation
Rename to `camelCase`




## `transfer` as reentrancy mitigation
### Summary
Fixed gas cost are not good reentrancy mitigations as the cost may change by the time.
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantPoolBase.sol#L86
            token.transfer(msg.sender, amount);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/liquid-staking/GiantSavETHVaultPool.sol#L108
        getDETH().transfer(msg.sender, dETHReceivedFromAllSavETHVaults);

https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L275
            bool transferResult = sETH.transfer(_sETHRecipient, _sETHAmount);


### Mitigation
Avoid using `transfer` fixed cost as a reentrancy mitigation as the gas cost may change.


## Unused named returns
### Summary
Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity 
### Details
Also as returns variable is ignored, it wastes extra gas

### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/a0558ed7b12e1ace1fe5c07970c7fc07eb00eebd/contracts/liquid-staking/LiquidStakingManager.sol#L921

### Mitigation
Remove return or returns when both used


##  Use of magic numbers is confusing and risky
### Summary
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details
Values are hardcoded and would be more readable and maintainable if declared as a constant

### Github Permalinks
- `4 ether`, `24 ether`
https://github.com/code-423n4/2022-11-stakehouse/blob/a0558ed7b12e1ace1fe5c07970c7fc07eb00eebd/contracts/liquid-staking/LiquidStakingManager.sol#L934-L945


### Mitigation
Replace magic hardcoded numbers with declared constants.

## Open TODOs   
### Summary
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
### Details
The code includes a `TODO` that affects readability and focus on the readers/auditors of the contracts
### Github Permalinks
https://github.com/code-423n4/2022-11-stakehouse/blob/23c3cf65975cada7fd2255a141b359a6b31c2f9c/contracts/syndicate/Syndicate.sol#L195
### Mitigation
Remove `TODO` or solved it


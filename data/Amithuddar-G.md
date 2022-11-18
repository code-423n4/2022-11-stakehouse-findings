1)++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops

File: 2022-11-stakehouse\contracts\liquid-staking\ETHPoolLPFactory.sol
  63,9:         for (uint256 i; i < numOfRotations; ++i) {
  
File: 2022-11-stakehouse\contracts\liquid-staking\GiantMevAndFeesPool.sol
  38,9:         for (uint256 i; i < numOfVaults; ++i) {
  64,9:         for (uint256 i; i < numOfVaults; ++i) {
  90,9:         for (uint256 i; i < _stakingFundsVaults.length; ++i) {
  117,9:         for (uint256 i; i < numOfRotations; ++i) {
  135,9:         for (uint256 i; i < numOfVaults; ++i) {
  183,9:         for (uint256 i; i < _lpTokens.length; ++i) {

File: 2022-11-stakehouse\contracts\liquid-staking\GiantPoolBase.sol
  76,9:         for (uint256 i; i < amountOfTokens; ++i) {  

File: 2022-11-stakehouse\contracts\liquid-staking\GiantSavETHVaultPool.sol
  42,9:         for (uint256 i; i < numOfSavETHVaults; ++i) {
  78,9:         for (uint256 i; i < numOfVaults; ++i) {
  82,13:             for (uint256 j; j < _lpTokens[i].length; ++j) {
  128,9:         for (uint256 i; i < numOfRotations; ++i) {
  146,9:         for (uint256 i; i < numOfVaults; ++i) {
  148,13:             for (uint256 j; j < _lpTokens[i].length; ++j) {  

File: 2022-11-stakehouse\contracts\liquid-staking\LiquidStakingManager.sol
  538,9:         for (uint256 i; i < numOfValidators; ++i) {
  587,9:         for (uint256 i; i < numOfKnotsToProcess; ++i) {  


File: 2022-11-stakehouse\contracts\liquid-staking\SavETHVault.sol
  63,9:         for (uint256 i; i < numOfValidators; ++i) {
  103,9:         for (uint256 i; i < numOfTokens; ++i) {
  116,9:         for (uint256 i; i < numOfTokens; ++i) { 

File: 2022-11-stakehouse\contracts\liquid-staking\StakingFundsVault.sol
  78,9:         for (uint256 i; i < numOfValidators; ++i) {
  152,9:         for (uint256 i; i < numOfTokens; ++i) {
  166,9:         for (uint256 i; i < numOfTokens; ++i) {
  203,9:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  266,9:         for (uint256 i; i < _blsPublicKeys.length; ++i) {
  276,9:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  286,9:         for (uint256 i; i < _token.length; ++i) {

File: 2022-11-stakehouse\contracts\syndicate\Syndicate.sol
  211,9:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  259,9:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  301,9:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  346,9:         for (uint256 i; i < _blsPubKeys.length; ++i) {
  420,9:         for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
  513,21:                     for (uint256 i; i < numberOfCollateralisedSlotOwnersForKnot; ++i) {
  560,9:         for (uint256 i; i < knotsToRegister; ++i) {
  585,9:         for (uint256 i; i < _priorityStakers.length; ++i) {
  598,9:         for (uint256 i; i < _blsPublicKeys.length; ++i) {
  648,9:         for (uint256 i; i < _blsPubKeys.length; ++i) {

2)X = X + Y OR X = X - Y CAN BE USED INSTEAD OF X += Y OR X -= Y

x = x + y or x = x - y costs less gas than x += y or x -= y. For example, v += 27 can be changed to v = v + 27 in the following code.   

File: 2022-11-stakehouse\contracts\liquid-staking\GiantPoolBase.sol
  39,17:         idleETH += msg.value;

File: 2022-11-stakehouse\contracts\liquid-staking\LiquidStakingManager.sol
  770,47:         stakedKnotsOfSmartWallet[smartWallet] += 1;
  782,23:         numberOfKnots += 1;
  839,23:         numberOfKnots += 1;  
  
File: 2022-11-stakehouse\contracts\liquid-staking\SavETHVault.sol
  71,25:             totalAmount += amount;  
  
File: 2022-11-stakehouse\contracts\liquid-staking\StakingFundsVault.sol
  58,21:         totalShares += 4 ether;
  97,25:             totalAmount += amount;
  278,30:             totalAccumulated += previewAccumulatedETH(_user, token);
  287,30:             totalAccumulated += previewAccumulatedETH(_user, _token[i]);  
  
File: 2022-11-stakehouse\contracts\liquid-staking\SyndicateRewardsProcessor.sol
  65,30:                 totalClaimed += due;
  85,42:                 accumulatedETHPerLPShare += (unprocessed * PRECISION) / _numOfShares;  
  
File: 2022-11-stakehouse\contracts\syndicate\Syndicate.sol
  185,52:                 accumulatedETHPerFreeFloatingShare += _calculateNewAccumulatedETHPerFreeFloatingShare(freeFloatingUnprocessed);
  190,56:             accumulatedETHPerCollateralizedSlotPerKnot += collateralizedUnprocessed;
  225,37:             totalFreeFloatingShares += _sETHAmount;
  226,47:             sETHTotalStakeForKnot[_blsPubKey] += _sETHAmount;
  227,63:             sETHStakedBalanceForKnot[_blsPubKey][_onBehalfOf] += _sETHAmount;
  317,30:                 totalClaimed += unclaimedUserShare;
  430,36:                     currentAccrued +=
  511,108:                     accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] += unprocessedETHForCurrentKnot;
  521,112:                         accruedEarningPerCollateralizedSlotOwnerOfKnot[_blsPubKey][collateralizedOwnerAtIndex] +=
  558,33:         numberOfRegisteredKnots += knotsToRegister;
  658,30:                 totalClaimed += unclaimedUserShare;  
  
File: 2022-11-stakehouse\contracts\liquid-staking\GiantMevAndFeesPool.sol
  40,21:             idleETH -= _ETHTransactionAmounts[i];

File: 2022-11-stakehouse\contracts\liquid-staking\GiantPoolBase.sol
  57,17:         idleETH -= _amount;  
  
File: 2022-11-stakehouse\contracts\liquid-staking\GiantSavETHVaultPool.sol
  46,21:             idleETH -= transactionAmount;

File: 2022-11-stakehouse\contracts\liquid-staking\LiquidStakingManager.sol
  615,51:             stakedKnotsOfSmartWallet[smartWallet] -= 1; 

File: 2022-11-stakehouse\contracts\syndicate\Syndicate.sol
  269,41:                 totalFreeFloatingShares -= _sETHAmount;
  272,47:             sETHTotalStakeForKnot[_blsPubKey] -= _sETHAmount;
  273,62:             sETHStakedBalanceForKnot[_blsPubKey][msg.sender] -= _sETHAmount;
  621,33:         totalFreeFloatingShares -= sETHTotalStakeForKnot[_blsPublicKey];
  624,33:         numberOfRegisteredKnots -= 1;  
  
3)Multiple address mappings can be combined into a single mapping of an address to a struct

Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

File: 2022-11-stakehouse\contracts\liquid-staking\ETHPoolLPFactory.sol
  44,5:     mapping(bytes => LPToken) public lpTokenForKnot;
  47,5:     mapping(LPToken => bytes) public KnotAssociatedWithLPToken;

File: 2022-11-stakehouse\contracts\liquid-staking\LiquidStakingManager.sol
  122,17:     /// @notice mapping to store if a node runner is whitelisted
  123,5:     mapping(address => bool) public isNodeRunnerWhitelisted;
  126,5:     mapping(address => address) public smartWalletRepresentative;
  129,5:     mapping(bytes => address) public smartWalletOfKnot;
  132,5:     mapping(address => address) public smartWalletOfNodeRunner;
  135,5:     mapping(address => address) public nodeRunnerOfSmartWallet;
  138,5:     mapping(address => uint256) public stakedKnotsOfSmartWallet;
  141,5:     mapping(address => address) public smartWalletDormantRepresentative;
  145,5:     mapping(bytes => address) public bannedBLSPublicKeys;
  149,5:     mapping(address => bool) public bannedNodeRunners;

File: 2022-11-stakehouse\contracts\syndicate\Syndicate.sol
  90,5:     mapping(bytes => bool) public isKnotRegistered;
  96,5:     mapping(address => bool) public isPriorityStaker;
  99,5:     mapping(bytes => uint256) public sETHTotalStakeForKnot;
  102,5:     mapping(bytes => mapping(address => uint256)) public sETHStakedBalanceForKnot;
  102,22:     mapping(bytes => mapping(address => uint256)) public sETHStakedBalanceForKnot;
  105,5:     mapping(bytes => mapping(address => uint256)) public sETHUserClaimForKnot;
  105,22:     mapping(bytes => mapping(address => uint256)) public sETHUserClaimForKnot;
  108,5:     mapping(bytes => uint256) public totalETHProcessedPerCollateralizedKnot;
  111,5:     mapping(bytes => mapping(address => uint256)) public accruedEarningPerCollateralizedSlotOwnerOfKnot;
  111,22:     mapping(bytes => mapping(address => uint256)) public accruedEarningPerCollateralizedSlotOwnerOfKnot;
  114,5:     mapping(bytes => mapping(address => uint256)) public claimedPerCollateralizedSlotOwnerOfKnot;
  114,22:     mapping(bytes => mapping(address => uint256)) public claimedPerCollateralizedSlotOwnerOfKnot;
  117,5:     mapping(bytes => bool) public isNoLongerPartOfSyndicate;
  120,5:     mapping(bytes => uint256) public lastAccumulatedETHPerFreeFloatingShare; 
  
4)Use assembly to check for address(0)
Impact

File: 2022-11-stakehouse\contracts\liquid-staking\ETHPoolLPFactory.sol
  77,41:         require(address(_oldLPToken) != address(0), "Zero address");
  78,41:         require(address(_newLPToken) != address(0), "Zero address");
  117,32:         if(address(lpToken) != address(0)) {

File: 2022-11-stakehouse\contracts\liquid-staking\GiantLP.sol
  40,47:         if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).beforeTokenTransfer(_from, _to, _amount);
  46,47:         if (address(transferHookProcessor) != address(0)) ITransferHookProcessor(transferHookProcessor).afterTokenTransfer(_from, _to, _amount);
  
File: 2022-11-stakehouse\contracts\liquid-staking\GiantMevAndFeesPool.sol
  151,22:         if (_from != address(0)) {  

File: 2022-11-stakehouse\contracts\liquid-staking\LiquidStakingManager.sol
  209,32:         require(smartWallet != address(0), "No wallet found");
  240,32:         require(_newAddress != address(0), "Zero address");
  279,32:         require(_nodeRunner != address(0), "Zero address");
  290,39:         require(_newRepresentative != address(0), "Zero address");
  294,32:         require(smartWallet != address(0), "No smart wallet");
  309,39:         require(_newRepresentative != address(0), "Zero address");
  312,32:         require(smartWallet != address(0), "No smart wallet");
  327,31:         require(_recipient != address(0), "Zero address");
  357,25:         require(_new != address(0) && _current != _new, "New is zero or current");
  360,27:         require(wallet != address(0), "Wallet does not exist");
  364,43:         require(newRunnerCurrentWallet == address(0), "New runner has a wallet");
  387,31:         require(_recipient != address(0), "Zero address");
  390,32:         require(smartWallet != address(0), "Unknown node runner");
  441,27:         if(smartWallet == address(0)) {
  454,54:         if(smartWalletRepresentative[smartWallet] != address(0)) {
  496,58:         return smartWalletOfKnot[_blsPublicKeyOfKnot] != address(0);
  501,116:         return !isBLSPublicKeyPartOfLSDNetwork(_blsPublicKeyOfKnot) || bannedBLSPublicKeys[_blsPublicKeyOfKnot] != address(0);
  544,46:             require(associatedSmartWallet != address(0), "Unknown BLS public key");
  563,34:             if(representative != address(0)) {
  658,25:         require(_dao != address(0), "Zero address");
  659,38:         require(_syndicateFactory != address(0), "Zero address");
  660,40:         require(_smartWalletFactory != address(0), "Zero address");
  661,27:         require(_brand != address(0), "Zero address");
  685,32:         require(_nodeRunner != address(0), "Zero address");
  700,70:         if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) {
  717,74:         else if(_isEnabled && smartWalletRepresentative[_smartWallet] == address(0)) {
  854,36:         if (address(gatekeeper) != address(0)) {
  939,44:         require(address(stakingFundsLP) != address(0), "No funds staked in staking funds vault");
  943,43:         require(address(savETHVaultLP) != address(0), "No funds staked in savETH vault");

File: 2022-11-stakehouse\contracts\liquid-staking\LPToken.sol
  62,47:         if (address(transferHookProcessor) != address(0)) transferHookProcessor.beforeTokenTransfer(_from, _to, _amount);
  69,47:         if (address(transferHookProcessor) != address(0)) transferHookProcessor.afterTokenTransfer(_from, _to, _amount);

File: 2022-11-stakehouse\contracts\liquid-staking\LPTokenFactory.sol
  19,43:         require(_lpTokenImplementation != address(0), "Address cannot be zero");
  33,39:         require(address(_deployer) != address(0), "Zero address");  
  
File: 2022-11-stakehouse\contracts\liquid-staking\LSDNFactory.sol
  51,56:         require(_liquidStakingManagerImplementation != address(0), "Zero Address");
  52,38:         require(_syndicateFactory != address(0), "Zero Address");
  53,36:         require(_lpTokenFactory != address(0), "Zero Address");
  54,40:         require(_smartWalletFactory != address(0), "Zero Address");
  55,27:         require(_brand != address(0), "Zero Address");
  56,41:         require(_savETHVaultDeployer != address(0), "Zero Address");
  57,47:         require(_stakingFundsVaultDeployer != address(0), "Zero Address");
  58,48:         require(_optionalGatekeeperDeployer != address(0), "Zero Address");  

File: 2022-11-stakehouse\contracts\liquid-staking\SavETHVault.sol
  206,33:         require(_smartWallet != address(0), "Zero address");
  236,49:         require(_liquidStakingManagerAddress != address(0), "Zero address");
  237,45:         require(address(_lpTokenFactory) != address(0), "Zero address");  
  
File: 2022-11-stakehouse\contracts\liquid-staking\StakingFundsVault.sol
  86,42:             if (address(tokenForKnot) != address(0)) {
  127,38:         if (address(tokenForKnot) != address(0)) {
  154,39:             require(address(token) != address(0), "No ETH staked for specified BLS key");
  177,38:         require(address(_lpToken) != address(0), "Zero address specified");
  229,39:             require(address(token) != address(0), "Invalid BLS key");
  242,28:         require(_wallet != address(0), "Zero address");
  317,26:         if (syndicate != address(0)) {
  332,30:                 if (_from != address(0)) {
  344,87:         if (LiquidStakingManager(payable(liquidStakingNetworkManager)).syndicate() != address(0)) {
  361,31:         require(_syndicate != address(0), "Invalid configuration");
  372,58:         require(address(_liquidStakingNetworkManager) != address(0), "Zero Address");
  373,45:         require(address(_lpTokenFactory) != address(0), "Zero Address");  
  
File: 2022-11-stakehouse\contracts\liquid-staking\SyndicateRewardsProcessor.sol
  57,31:         require(_recipient != address(0), "Zero address");

File: 2022-11-stakehouse\contracts\smart-wallet\OwnableSmartWallet.sol
  34,29:             initialOwner != address(0),
  116,19:             to != address(0),

File: 2022-11-stakehouse\contracts\smart-wallet\OwnableSmartWalletFactory.sol
  37,26:         require(owner != address(0), 'Wallet cannot be address 0');

File: 2022-11-stakehouse\contracts\syndicate\Syndicate.sol
  206,28:         if (_onBehalfOf == address(0)) revert ZeroAddress();
  231,31:             if (stakeHouse == address(0)) revert KnotIsNotAssociatedWithAStakeHouse();
  253,39:         if (_unclaimedETHRecipient == address(0)) revert ZeroAddress();
  254,31:         if (_sETHRecipient == address(0)) revert ZeroAddress();
  295,27:         if (_recipient == address(0)) revert ZeroAddress();
  642,27:         if (_recipient == address(0)) revert ZeroAddress();  

 
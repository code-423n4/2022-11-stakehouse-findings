
##  [1] EVENT IS MISSING INDEXED FIELDS

### Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

> There are  instances of this issue:


         FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

         16:   event ETHWithdrawnByDepositor(address depositor, uint256 amount);
        
         19:   event LPTokenBurnt(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);

         22:   event NewLPTokenIssued(bytes blsPublicKeyOfKnot, address token, address firstDepositor, uint256 amount);

         25:   event LPTokenMinted(bytes blsPublicKeyOfKnot, address token, address depositor, uint256 amount);


###

##  [2] public MODIFIER CAN BE CHANGED TO internal in **rotateLPTokens()** .  rotateLPTokens() IS USED WITHIN THE FUNCTIONS. IF WE DECLARE  rotateLPTokens() AS A public  CAN BE ACCESSIBLE FROM OUTSIDE OF THE SMART CONTRACTS.

              FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol
 
              76:  function rotateLPTokens(LPToken _oldLPToken, LPToken _newLPToken, uint256 _amount) public {
              

 ###

##  [3] CAN CHECK MORE FAILURE POSSIBILITY CONDITIONS FIRST INSTEAD OF address(0) CHECK . FOR address(0) CONDITIONS FAILURE POSSIBILITY I IS LESS COMPARE TO OTHER require CONDITION CHECKS . IN THIS WAY WE CAN AVOID TO CHECK SOME REQUIRE STATEMENTS CHECKS . WE CAN CHECK address(0) CHECK IN THE LAST 

         FILE:  2022-11-stakehouse/contracts/liquid-staking/ETHPoolLPFactory.sol

        require(address(_oldLPToken) != address(0), "Zero address");
        require(address(_newLPToken) != address(0), "Zero address");
        require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
        require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
        require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");

Proof Of Work:  

        require(_oldLPToken != _newLPToken, "Incorrect rotation to same token");
        require(_amount >= MIN_STAKING_AMOUNT, "Amount cannot be zero");
        require(_amount <= _oldLPToken.balanceOf(msg.sender), "Not enough balance");
        require(_oldLPToken.lastInteractedTimestamp(msg.sender) + 30 minutes < block.timestamp, "Liquidity is still fresh");
        require(_amount + _newLPToken.totalSupply() <= 24 ether, "Not enough mintable tokens");
        require(address(_oldLPToken) != address(0), "Zero address");
        require(address(_newLPToken) != address(0), "Zero address");


##

##  [4]   USUAL SUSPECTS: LACK OF ZERO CHECKS FOR NEW ADDRESSES

###  Constructors don’t have zero-checks, which could force a re-deployment, funds are not at risk in those cases.


        FILE: 2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol 

       constructor(
        address _pool,
        address _transferHookProcessor,
        string memory _name,
        string memory _symbol
        ) ERC20(_name, _symbol) {
        pool = _pool;
        transferHookProcessor = ITransferHookProcessor(_transferHookProcessor);
       }

##

## [5] REQUIRE STATEMENTS THROUGHS WRONG OR MEANING LESS  ERROR COMMANDS 

      2022-11-stakehouse/contracts/liquid-staking/GiantLP.sol

      30:    require(msg.sender == pool, "Only pool");

      35:    require(msg.sender == pool, "Only pool");

##

## 

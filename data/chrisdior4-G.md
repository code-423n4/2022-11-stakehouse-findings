# [G-01] x += y COSTS MORE GAS THAN  x= x + y FOR STATE VARIABLES

 

Using the addition operator instead of plus-equals saves 113 gas

 

File: GiantPoolBase

 

idleETH += msg.value;          -

idleETH = idleETH + msg.value; +

 

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L39

 

===========================

 

idleETH -= _amount;            -

idleETH00 = idleETH - _amount; +

 

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L57

 

===========================

 

File: GiantSavETHVaultPool.sol

 

idleETH -= transactionAmount;          -

idleETH = idleETH - transactionAmount; +

 

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L46

 

==========================

File: LiquidStakingManager.sol

 

numberOfKnots += 1;                -

numberOfKnots = numberOfKnots + 1; +

 

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L782

 

========================

 

numberOfKnots += 1;                 -

numberOfKnots = numberOfKnots + 1;  +

 

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L839

 

===========================

 

# [G-02]  ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

 

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop:

 

 

for(uint256 i; i < _blsPubKeys.length; ++i) {

 

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L392

 

===========================

 

for(uint256 i; i < len; ++i)

 

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/LiquidStakingManager.sol#L465

 # [G-03] COMPARISONS: BOOLEAN COMPARISONS

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using:

if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here:


if (isKnotRegistered[_blsPublicKey] == false) revert KnotIsNotRegisteredWithSyndicate();

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L611

if (isNoLongerPartOfSyndicate[_blsPublicKey] == true) revert KnotHasAlreadyBeenDeRegistered();

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L612

# [G-04] Division by two should use bit shifting

<x> / 2 is the same as <x> >> 1. The DIV opcode costs 5 gas, whereas SHR only costs 3 gas

return ethPerKnot / 2;

- https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L378

Consider changing it to:

return ethPerKnot >> 1;

# [G-05 ]  ADDRESS MAPPINGS CAN BE COMBINED IN A SINGLE MAPPING
Combining mappings of address into a single mapping of address to a struct can save a Gssset (20000 gas) operation per mapping combined. This also makes it cheaper for functions reading and writing several of these mappings by saving a Gkeccak256 operation- 30 gas.

File: LiquidStakingManager

mapping(address => bool) public isNodeRunnerWhitelisted;
mapping(address => address) public smartWalletRepresentative;
mapping(address => address) public smartWalletOfNodeRunner;
mapping(address => address) public nodeRunnerOfSmartWallet;
mapping(address => uint256) public stakedKnotsOfSmartWallet;
mapping(address => address) public smartWalletDormantRepresentative;
mapping(address => bool) public bannedNodeRunners;

Change it to:

 struct Hold {
    bool isNodeRunnerWhitelisted;
    address  smartWalletRepresentative;
    address smartWalletOfNodeRunner;
    address nodeRunnerOfSmartWallet;
    uint256 stakedKnotsOfSmartWallet;
    address smartWalletDormantRepresentative;
    bool  bannedNodeRunners;
 }
 mapping (address => Hold) public holds;

# [G‑06] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are
CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost:

function registerKnotsToSyndicate(
        bytes[] calldata _newBLSPublicKeyBeingRegistered
    ) external onlyOwner {

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/syndicate/Syndicate.sol#L145

 function deRegisterKnots(bytes[] calldata _blsPublicKeys) external onlyOwner {
        updateAccruedETHPerShares();
        _deRegisterKnots(_blsPublicKeys);
    }


- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/syndicate/Syndicate.sol#L154

function addPriorityStakers(address[] calldata _priorityStakers) external onlyOwner {
        updateAccruedETHPerShares();
        _addPriorityStakers(_priorityStakers);
    }

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/syndicate/Syndicate.sol#L161


function updatePriorityStakingBlock(uint256 _endBlock) external onlyOwner {
        updateAccruedETHPerShares();
        priorityStakingEndBlock = _endBlock;
    }

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/syndicate/Syndicate.sol#L168



function setApproval(address to, bool status) external onlyOwner override {
        require(
            to != address(0),
            "OwnableSmartWallet: Approval cannot be set for zero address"
        ); // F: [OSW-2A]
        _setApproval(msg.sender, to, status);
    }

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/smart-wallet/OwnableSmartWallet.sol#L114


# [G‑07] Using bools for storage incurs overhead

    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from false to true, after having been true in the past:

bool _deployOptionalGatekeeper,

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/interfaces/ILiquidStakingManager.sol#L24

bool status 
);

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/smart-wallet/interfaces/IOwnableSmartWallet.sol#L8

bool withdrawn;

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/SavETHVault.sol#L36

mapping(address => bool) public isLiquidStakingManager;

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/LSDNFactory.sol#L39

bool _deployOptionalHouseGatekeeper,

- https://github.com/code-423n4/2022-11-stakehouse/blob/39a3a84615725b7b2ce296861352117793e4c853/contracts/liquid-staking/LSDNFactory.sol#L76


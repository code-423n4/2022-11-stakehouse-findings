### missing natspec 

some files were found in scope tht did not contain any natspec making it difficult to understand how they incooperate into the project files without reading docs etc

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalGatekeeperFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol


### The following Files do not follow standard solidity style conventions or have inconsistant layout.

All of the follwing have the SPDX license identifier specified after the pragma version, where as the rest of the project files specify the SPDX first consier moving the SPDX above the pragma version declaration like has been done with the rest of the project contracts in order to maintain some consitancy with regard to layout and readability.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LPTokenFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LSDNFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantSavETHVaultPool.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol 

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

### No 0 address check on constructors

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/OptionalHouseGatekeeper.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SavETHVaultDeployer.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVaultDeployer.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/smart-wallet/OwnableSmartWalletFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateFactory.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantMevAndFeesPool.sol

Consider adding 0 address checks so that no 0 address's can be set at deployment as has been done in LPTokenFactory constructor.

### Unused payable function 

it may be possible for ETH to be locked into the contract with now way to retrieve it back other than through the protocol, this could lead to loss of funds if user was to input more funds then intended to the contract, add a way to retrieve plan ETH from the contract also to mitigate this if allowing a way of plan ETH to be transfered to the contract.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/LiquidStakingManager.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/SyndicateRewardsProcessor.sol
https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantLP.sol

   receive() external payable {
        // receive ETH
    }

### Open TODO
Consider removing this open TODO comment and carrying out a sanity check to check this else case prior to doing so

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L195

         // todo - check else case for any ETH lost


### No time based logic to switching critical address


switching this address without informing users could suprise some users and leave them no time to react, add some tiem based logic so there is time for users to react to the change or at least be informed of the change before it occurs.

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L239

    function updateDAOAddress(address _newAddress) external onlyDAO {
        require(_newAddress != address(0), "Zero address");
        require(_newAddress != dao, "Same address");

        emit UpdateDAOAddress(dao, _newAddress);

        dao = _newAddress;
    }


### No SPDX license

the following file was found to contain no SPDX license identifier consider adding one so the general public are aware under what license this file can be used

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/SyndicateErrors.sol

### Bool return value unchecked

on ERC20 transfer the bool return value for a succesful transfer is not checked, it is best practive to check all return values, consider adding the return(success) to this tranfer function, this can create  undersirable reverts on transfers including but no limited to loss of funds.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/GiantPoolBase.sol

### Reentrance modifier 

Add a reentrance modifier to this function to ensure that reentrancy is not possible, as appose to creating your own reentrancy modifier using your own checks on balance differences, use one that has been tried and tested to stop these kinds of attacks.....qustion is raised can tokens be transfered and the function called again with 0 balance in the same transaction to gain more tokens before aftertransfer is called. a tried and tested reentrancy gaurd should be used even if this isnt the case to stop unsavoury characters trying to do so by balance manipulation etc between contracts and function calls.

https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard-nonReentrant--

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/liquid-staking/StakingFundsVault.sol

### Empty else body

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L194

As it does not appear that it has been completed by the dev, the function currently has an empty else body, which could mean if it reaches this piece of code, the update could silenlty fail to updateAccruedETHPerShares, leading to the user possibly thinking the update has gone through but it actually not, this could lead to users also recieving the wrong accruedETHPerShares if indeed this value was needed to be updated to more or less than the current value.



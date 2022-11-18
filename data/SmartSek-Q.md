# Low Risk Findings

## L[01] - No check if `EOARepresentative` or `EOARepresentativeOfNodeRunner` is an EOA or a smart contract

## Impact
A smart contract can end up being assigned as a `smartWalletRepresentative`.
Such smart contract might not have the functionality to interact with LSD or funds.

## Proof of Concept
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L289-L303

```solidity
  function rotateEOARepresentative(address _newRepresentative) external {
        require(_newRepresentative != address(0), "Zero address");
        require(isNodeRunnerBanned(msg.sender) == false, "Node runner is banned from LSD network");

        address smartWallet = smartWalletOfNodeRunner[msg.sender];
        require(smartWallet != address(0), "No smart wallet");
        require(stakedKnotsOfSmartWallet[smartWallet] == 0, "Not all KNOTs are minted");
        require(smartWalletRepresentative[smartWallet] != _newRepresentative, "Invalid rotation to same EOA");

        // unauthorize old representative
        _authorizeRepresentative(smartWallet, smartWalletRepresentative[smartWallet], false);

        // authorize new representative
        _authorizeRepresentative(smartWallet, _newRepresentative, true);
    }
```

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L695-L736

```solidity
    function _authorizeRepresentative(
        address _smartWallet, 
        address _eoaRepresentative, 
        bool _isEnabled
    ) internal {
        if(!_isEnabled && smartWalletRepresentative[_smartWallet] != address(0)) {

            // authorize the EOA representative on the Stakehouse
            IOwnableSmartWallet(_smartWallet).execute(
                address(getTransactionRouter()),
                abi.encodeWithSelector(
                    ITransactionRouter.authorizeRepresentative.selector,
                    _eoaRepresentative,
                    _isEnabled
                )
            );

            // delete the mapping
            delete smartWalletRepresentative[_smartWallet];

            emit RepresentativeRemoved(_smartWallet, _eoaRepresentative);
        }
        else if(_isEnabled && smartWalletRepresentative[_smartWallet] == address(0)) {

            // authorize the EOA representative on the Stakehouse
            IOwnableSmartWallet(_smartWallet).execute(
                address(getTransactionRouter()),
                abi.encodeWithSelector(
                    ITransactionRouter.authorizeRepresentative.selector,
                    _eoaRepresentative,
                    _isEnabled
                )
            );

            // store EOA to the wallet mapping
            smartWalletRepresentative[_smartWallet] = _eoaRepresentative;

            emit RepresentativeAppointed(_smartWallet, _eoaRepresentative);
        } else {
            revert("Unexpected state");
        }
    }
```

### Recommended Mitigation Steps
Add a check like the one seen here:
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L435-L436

```solidity
        require(!Address.isContract(_eoaRepresentative), "Only EOA representative permitted");

```

## L[02] - Single-step `DAOAddress` transfer is unsafe

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/LiquidStakingManager.sol#L239-L246

```solidity
    function updateDAOAddress(address _newAddress) external onlyDAO {
        require(_newAddress != address(0), "Zero address");
        require(_newAddress != dao, "Same address");

        emit UpdateDAOAddress(dao, _newAddress);

        dao = _newAddress;
    }
```

If `dao` is set to the incorrect address, all the functionality vetted by `onlyDAO` modifier  will be bricked.
Please implement a two-step process.




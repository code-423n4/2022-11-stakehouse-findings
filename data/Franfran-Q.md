# 1 _addPriorityStakers() doesn't enforce duplicate array elements

## Issue

`_addPriorityStakers()` doesn't revert if array elements are equal when next to each other.
Even though this can be written to publicly (when deploying a syndicate with `deploySyndicate` in `SyndicateFactory.sol`), they are no side effect as the same staker will only be written to `true` twice in the `isPriorityStaker` mapping, so submitted as low severity.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L583

## POC

```solidity
contract Syndicate is Test {
    function _addPriorityStakers(address[] memory _priorityStakers) internal {
        if (_priorityStakers.length == 0) revert EmptyArray();
        for (uint256 i; i < _priorityStakers.length; ++i) {
            address staker = _priorityStakers[i];

            if (i > 0 && staker < _priorityStakers[i-1]) revert DuplicateArrayElements();

            // isPriorityStaker[staker] = true;

            // emit PriorityStakerRegistered(staker);
        }
    }

    address r1 = address(123456789);
    address r2 = address(12345678910);

    function testDuplicate() public {
        address[] memory s1 = new address[](0);
        vm.expectRevert(EmptyArray.selector);
        _addPriorityStakers(s1);

        address[] memory s2 = new address[](2);
        s2[0] = r1;
        s2[1] = r2;
        // normal behaviour, shouldn't revert
        _addPriorityStakers(s2);

        address[] memory s3 = new address[](3);
        s3[0] = r1;
        s3[1] = r2;
        s3[2] = r1;
        // duplicate case on address not next to each other, should revert
        vm.expectRevert(DuplicateArrayElements.selector);
        _addPriorityStakers(s3);

        address[] memory s4 = new address[](3);
        s4[0] = r1;
        s4[1] = r2;
        s4[2] = r2;
        // should revert, but does not
        _addPriorityStakers(s4);
    }
}
```

## Remedy

Should use `if (i > 0 && staker <= _priorityStakers[i-1]) revert DuplicateArrayElements();` instead.

# 2 Nobody can get whitelisted in LSD manager

## Issue

In the `updateNodeRunnerWhitelistStatus()` function, nobody can get whitelisted because of a wrong checking.
It should instead check from the function input arguments.

## POC

```solidity
    function setUp() public {
        vm.startPrank(accountFive); // this will mean it gets dETH initial supply
        factory = createMockLSDNFactory();
        vm.stopPrank();

        // Deploy 1 network and get default dependencies
        manager = deployNewLiquidStakingNetwork(
            factory,
            admin,
            true,
            "LSDN"
        );

        savETHVault = getSavETHVaultFromManager(manager);
        stakingFundsVault = getStakingFundsVaultFromManager(manager);

        // make 'admin' the 'DAO'
        vm.prank(address(factory));
        manager.updateDAOAddress(admin);
    }

    function testCannotWhitelist() public {
        // Set up users and ETH
        address nodeRunner = accountOne; vm.deal(nodeRunner, 4 ether);
        // Node runner is not yet whitelisted
        bool wl = manager.isNodeRunnerWhitelisted(nodeRunner);

        // Prank as DAO
        vm.startPrank(admin);

        // Check by banning
        vm.expectRevert(bytes("Unnecessary update to same status"));
        manager.updateNodeRunnerWhitelistStatus(nodeRunner, false);

        // Check with true ?
        vm.expectRevert(bytes("Unnecessary update to same status"));
        manager.updateNodeRunnerWhitelistStatus(nodeRunner, true);

        // Both revert
    }
```

## Remedy

```solidity
    function updateNodeRunnerWhitelistStatus(address _nodeRunner, bool isWhitelisted) external onlyDAO {
        require(_nodeRunner != address(0), "Zero address");
        // - require(isNodeRunnerWhitelisted[_nodeRunner] != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");
        + require(isWhitelisted != isNodeRunnerWhitelisted[_nodeRunner], "Unnecessary update to same status");

        isNodeRunnerWhitelisted[_nodeRunner] = isWhitelisted;
        emit NodeRunnerWhitelistingStatusChanged(_nodeRunner, isWhitelisted);
    }
```
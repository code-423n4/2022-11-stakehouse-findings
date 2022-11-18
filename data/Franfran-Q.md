# Issue

`_addPriorityStakers()` doesn't revert if array elements are equal when next to each other.
Even though this can be written to publicly (when deploying a syndicate with `deploySyndicate` in `SyndicateFactory.sol`), they are no side effect as the same staker will only be written to `true` twice in the `isPriorityStaker` mapping, so submitted as low severity.

https://github.com/code-423n4/2022-11-stakehouse/blob/main/contracts/syndicate/Syndicate.sol#L583

# POC

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

# Remedy

Should use `if (i > 0 && staker <= _priorityStakers[i-1]) revert DuplicateArrayElements();` instead.
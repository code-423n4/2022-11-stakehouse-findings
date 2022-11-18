# QA findings (Low risk or Non-critical)
  
  - [Low (L) and  Non-critical (N)](#low-l-and--non-critical-n)
  - [[L-1] using `assume` in fuzzing can be misleading](#l-1-using-assume-in-fuzzing-can-be-misleading)
  - [[L-2] use of erc 1155 instead of deploying new LP token](#l-2-use-of-erc-1155-instead-of-deploying-new-lp-token)
  - [[N-1] public functions not called by the contract should be declared external instead](#n-1-public-functions-not-called-by-the-contract-should-be-declared-external-instead)
  - [[N-2] dangerous use of low level `call`](#n-2-dangerous-use-of-low-level-call)
  - [[N-3] remaining todo](#n-3-remaining-todo)
  - [[N-4] typos](#n-4-typos) 



## [L-1] using `assume` in fuzzing can be misleading 
foundry fuzzing `fuzz.runs` is set to 500 runs although `fuzz.max_test_rejects` default is 65535 therefor all the inputs can be rejected and the test will still pass.  
try creating a foundry.toml file with this content and run `forge test -vv`

```toml
[fuzz] 
max_test_rejects = 499
```
you will have one failing test

```
[FAIL. Reason: The `vm.assume` cheatcode rejected too many inputs (499 allowed)] testFirstDepositsForKnotAreBetweenOneAndTwentyFour(uint256) (runs: 3, μ: 549378, ~: 549378)
```

for uint256 input like this it is recommended to use `bound` instead

```
 stakeAmount = bound(stakeAmount, 0.001 ether, 24 ether);
```
## [L-2] use of erc 1155 instead of deploying new LP token 

Each bls is associated with a unique LP token. Each time we deploy a new LPToken contract and we concatenate the LP count to the name.
This is expensive and it is difficult to keep track of all the contracts deployed by the factory.
We can achieve the same by using a single `ERC-1155` contract . We simply mint a new LP by calling the `mint` function with the new ID which can also be the LP count.  

here is the code that can be replace in `ETHPoolLPFActory.sol` inside the `_depositETHForStaking` function
```solidity
 // mint new LP tokens for the new KNOT
            // add the KNOT in the mapping
            string memory tokenNumber = Strings.toString(numberOfLPTokensIssued);
            string memory tokenName = string(abi.encodePacked(baseLPTokenName, tokenNumber));
            string memory tokenSymbol = string(abi.encodePacked(baseLPTokenSymbol, tokenNumber));

            // deploy new LP token and optionally enable transfer notifications
            LPToken newLPToken = _enableTransferHook ?
                             LPToken(lpTokenFactory.deployLPToken(address(this), address(this), tokenSymbol, tokenName)) :
                             LPToken(lpTokenFactory.deployLPToken(address(this), address(0), tokenSymbol, tokenName));

            // increase the count of LP tokens
            numberOfLPTokensIssued++;
```


## [N-1] public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.
```solidity
contracts/liquid-staking/StakingFundsVault.sol:
239: function withdrawETH(address \_wallet, uint256 \_amount) public onlyManager nonReentrant returns (uint256) {
```


## [N-2] dangerous use of low level `call`
You should avoid using .call() whenever possible when executing another contract function as it bypasses type checking, function existence check, and argument packing. the use of `payable(msg.sender).transfer(_amount);` is recommended.

 
## [N-3] remaining todo 

```solidity
contracts/syndicate/Syndicate.sol:
  195:             // todo - check else case for any ETH lost
```


## [N-4] typos

`depoistor` instead of `depositor`
```
contracts/liquid-staking/ETHPoolLPFactory.sol:
  124:             // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied
  150:             // mint LP tokens for the depoistor with 1:1 ratio of LP tokens and ETH supplied
```

 
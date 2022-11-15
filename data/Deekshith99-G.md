## [G-1] Division of 2 should use Bit shifting
      Here is the one instance of it https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/syndicate/Syndicate.sol#L378
      <x> / 2 is the same as <x> >> 1. While the compiler uses the SHR opcode to accomplish both, the version that uses division incurs an overhead of 20 
      gas due to JUMPs to and from a compiler utility function that introduces checks which can be avoided by using unchecked {} around the division by 
      two
## [G-2] Use of recent solidity version
      it can be optimized and saves some gas 

## [G-3] unwanted state variable declared
      here is the permalink of it 
      1e24 can directly used right ? why to declare it as PRECISION and calling it ? it can save more gas if it uses directly
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/SyndicateRewardsProcessor.sol#L15
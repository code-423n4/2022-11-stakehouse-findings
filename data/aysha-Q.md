Unhandled return values of transfer and transferFrom: ERC20 implementations are not always consistent. Some implementations of transfer and transferFrom could return ‘false’ on failure instead of reverting. It is safer to wrap such calls into require() statements to these failures.

Recommendation: Check the return value and revert on 0/false or use OpenZeppelin’s SafeERC20 wrapper functions

Medium severity finding from Consensys Diligence Audit of Aave Protocol V2: https://consensys.net/diligence/audits/2020/09/aave-protocol-v2/#unhandled-return-values-of-transfer-and-transferfrom

https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantMevAndFeesPool.sol#L184
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantPoolBase.sol#L86
https://github.com/code-423n4/2022-11-stakehouse/blob/4b6828e9c807f2f7c569e6d721ca1289f7cf7112/contracts/liquid-staking/GiantSavETHVaultPool.sol#L108
==========================================================

External calls in loop can lead to denial of service: Several function calls are made in unbounded loops. This pattern is error-prone as it can trap the contracts due to the gas limitations or failed transactions.

Recommendation: Short term, review all the loops mentioned above and either: 1) allow iteration over part of the loop, or 2) remove elements. Long term, subscribe to Crytic.io to review external calls in loops. Crytic catches bugs of this type.

High Risk severity finding from ToB’s Audit of Origin Dollar: https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L73
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L88

==========================================================

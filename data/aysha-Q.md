External calls in loop can lead to denial of service: Several function calls are made in unbounded loops. This pattern is error-prone as it can trap the contracts due to the gas limitations or failed transactions.

Recommendation: Short term, review all the loops mentioned above and either: 1) allow iteration over part of the loop, or 2) remove elements. Long term, subscribe to Crytic.io to review external calls in loops. Crytic catches bugs of this type.

High Risk severity finding from ToB’s Audit of Origin Dollar: https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L73
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L88

==========================================================

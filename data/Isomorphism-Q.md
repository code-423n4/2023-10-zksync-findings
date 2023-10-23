L

Profit will be negative

https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md#zksync-era-fee-components-revenue--costs

## Under most conditions profit will be negative

The above specs list definitions for revenue, profit and costs for the Zksync chain.

The profit term here is misleading. Running the transaction fees for L2 is quite costly. (In the last 24hr the zksync validator spent ~50ETH, other L2s like starkex also spent a similar amount). 

##Proof of concept

Lets assume that under ideal conditions Zksync will have 100x smaller transaction fees than ethereum. That means for every account doing a deposit/withdraw on the L2, they would have to do ~200 transactions on the L2 for the operators of L2 to break even on fees. 

Put in another way, for every unit of gas spent on the L1 by operators, an EOA must spend ~200 units on the L2 to break even.

This is an unrealistically large number of transactions for the average EOA account. This doesn't include any infrastructure costs.

It would be more accurate to call this operating expense within the specs.

Indeed, it is likely that the vast majority of the profit and value accrued to the L2 will come from other means (token appreciation, etc.).

## Mitigation

https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md#zksync-era-fee-components-revenue--costs

Change profit = L2 fees - L1 fees to operating cost = L1 fees - L2 fees and understand the long term costs.


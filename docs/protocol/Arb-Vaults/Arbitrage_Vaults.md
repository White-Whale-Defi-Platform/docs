# Arbitrage Exposure

The Arb Vaults are the initial main feature of the platform. Users deposit UST
into the UST Vault. This principal in the vault is then deposited into
Anchor protocol to generate yield. When UST is off peg the deposits are used by the
protocol to arbitrage UST back to peg. 
The total rewards distributed to depositors of the Arb Vault, which will be
reflected in the intrest earned, will be:

> Total Yield = Anchor Yield + Arbitrage Profits + Flashloan Income

Besides in-house arbitrage strategies, White Whale will offer the posibility to 
leverage the funds held inside the vault through flash loans. A small fee of X% will be
charged on the borrowed funds. The smart contract logic enshures all funds are returned in
the same transaction. A detailed description is provided in [this](https://medium.com/@whitewhaleterra/white-whale-releases-details-on-flash-loan-architecture-18cae06e5bef) Medium article.

Other asset vaults, such as the LUNA ARB vault, will be added
by the team as the development phase continues. Ultimately, the
community will decide which new asset vaults to add through governance
proposals

> Vault Fees:  
> Deposit - Anchor's Deposit Fee into Earn  
> Withdrawal - 0.1%  
> Arb Profit - 20% of any Arbitrage trade profit goes to Treasury  

Note: Treasury does NOT take any percentage of the Anchor Earn Yield.

## Arbitrage

Arbitrage is the simultaneous purchase and sale of the same asset in different markets in order to profit from differences in the asset's listed price. Arbitrage exists as a result of market inefficiencies and it both exploits those inefficiencies and resolves them.

White Whale currently takes advantage of arbitrage opportunities on Terra, specifically UST peg arbitrage, with plans to add more strategies and more chains. 

## Flash Loans

White Whale developed the first flash loans on Terra. 

Flash Loan Explained:

1. Call Flash Loan Smart Contract  
2. UST provided from the Vault 
3. User Actions 
4. UST paid back to Vault + Fee

Steps 1-4 steps all happen in 1 transaction.

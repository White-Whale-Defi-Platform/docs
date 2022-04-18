# Arbitrage Exposure

The Arbitrage Vaults are the main product of White Whale. 

Arbitrage is the simultaneous purchase and sale of the same asset in different markets in order to profit from differences in the asset's listed price. Arbitrage exists as a result of market inefficiencies and it both exploits those inefficiencies and resolves them.

White Whale currently takes advantage of arbitrage opportunities on Terra, specifically UST peg arbitrage, with plans to add more strategies and more chains. 
## UST Vault

Our flagship product is the UST Vault. The vault deposits the UST into Anchor protocol to generate a base yield. The vault then performs arbitrage for additional risk free yield on top of the Anchor yield. 

The UST vault's main purpose is to protect the UST peg through L1 seigniorage arbitrage, but also earns revenue from Multi-Dex Arbitrage and Flash Loans. 

**L1 Seigniorage Arbitrage: **   
Note: Validators must exchange 1 UST for a $1 worth of LUNA. 

When 1 UST < $1, exchange 1 UST for $1 worth of LUNA with validators sell LUNA on DEX.  
When 1 UST > $1, buy LUNA with UST on DEX, then exchange $1 worth of LUNA for 1 UST with validators.  

**Multi-Dex Arbitrage:  **  
Luna is trading at $99 dollars on Terraswap.   
Luna is trading at $100 dollars on Astroport.   
Buy Luna on Terraswap sell on Astroport. 

**Flash-Loans:  **  
White Whale offers the possibility to 
leverage the funds held inside the vault through flash loans. A small fee of .1% will be
charged on the borrowed funds. The smart contract logic ensures all funds are returned in
the same transaction. A detailed description is provided in [this](https://medium.com/@whitewhaleterra/white-whale-releases-details-on-flash-loan-architecture-18cae06e5bef) Medium article.

#### Yield 
> Total Vault Yield = Anchor Yield + Arbitrage Profits (L1 + Multi-Dex Arbitrage) + Flashloan Income

#### APR Calculation
We take average of vault tvl and profits for last 7 days and:   
1. 7daysAPR = (100 * weeklyProfit ) / tvlAverageLast7Days  
2. APR = (365 * 7daysAPR ) / 7  

#### Vault Fees

> Deposit - Anchor's Deposit Fee into Earn  
> Withdrawal - 0.1%  
> Arb Profit - 20% of any Arbitrage trade profit goes to Treasury  

Note: Treasury does NOT take any percentage of the Anchor Earn Yield.

## Other Vaults

Other asset vaults, such as the LUNA ARB vault, will be added
by the team as the development phase continues. Ultimately, the
community will decide which new asset vaults to add through governance proposals.







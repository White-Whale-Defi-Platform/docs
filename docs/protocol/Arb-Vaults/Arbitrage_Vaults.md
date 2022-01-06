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

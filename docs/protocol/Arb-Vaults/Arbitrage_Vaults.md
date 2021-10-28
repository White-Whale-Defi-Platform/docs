# Arbitrage Exposure

The Arb Vaults are the initial main feature of the platform. Users deposit UST
into UST ARB Vault. This principal in the ARB vault is then deposited into
Anchor protocol to generate yield. When UST is off peg above a threshold
level to account for transaction fees, then the deposits are used by the
protocol to arbitrage UST back to peg with the L1 seigniorage method9.  
The total rewards distributed to depositors of the Arb Vault, which will be
reflected in their UST balance in the vault, will be:

> R = Anchor Yield + Arbitrage Profits (+ aUST Optimized Yield Farm further into development)

The UST deposited into the Anchor Protocol for yield results in aUST that in
turn will be used to generate additional yield in the future. Two yield
strategies under consideration are the Delta-Neutral yield farming of MIR9
and stablecoin yield farming using MIM token10. Implementation of
augmented yield using aUST during flat markets likely will improve vault
rewards.
Other asset vaults, such as the LUNA ARB (LUNA-bLUNA) vault, will be added
by the team as the development phase continues. Ultimately, the
community will decide which new asset vaults to add through governance
proposals

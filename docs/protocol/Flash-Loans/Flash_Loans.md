# Flash Loans 

White Whale's development team worked to augment our original UST Vault concept to enable users on the network to avail of the first flash loans on Terra and possibly the first non-EVM implementation of flash loans.

The net effect of this is now funds which are provided by the community to keep the peg can also be utilized by the community as a composable DeFi "money lego" in the form of an uncollateralised loan product.

## Flash Loans Explained 

Flash Loans are special transactions that allow the borrowing of an asset, as long as the borrowed amount (and a fee) is returned before the end of the transaction (also called One Block Borrows). These transactions do not require a user to supply collateral prior to engaging in the transaction. There is no real world analogy to Flash Loans, so it requires some basic understanding of how state is managed within blocks in blockchains.
[Source - Aave](https://docs.aave.com/developers/guides/flash-loans)

Flash Loans provide an innovative way for users of the Terra network to take out uncollateralised loans with the caveat that the loan is taken out and paid back within the same transaction. At a high level, flash loans follow this flow :

1. Call Flash Loan on UST Vault
2. UST provided from the Vault to caller
3. Caller uses flash loan collateral for a series of actions
4. Caller calls repay functionality of the flash loan 

Steps 1-4 all happen in 1 transaction. The last step has some checks in place to ensure that overall the loan can be paid back in full with any fees but also that there is some degree of profit. If for example the repay functionality is not called or it is called with not enough funds, the entire flash loan with fail/revert and will have never been taken out. Instead only gas is spent.

## Applications of Flash Loans
White Whale Flash Loans are already used in part by the White Whale Protocol liquidity on large arbs. Other examples in the wild include:
+ Arbitrage between assets, without needing to have the principal amount to execute the arbitrage.
+ Liquidating borrow positions, without having to repay the debt of the positions and using discounted collateral claimed to payoff flashLoan amount + fee.
+ Arbitrage minting or burning of [Nebula Protocol Clusters]() to aid in the re-balancing of a given cluster capturing any delta as profit.

## How to use Flash Loans

### Calling the FlashLoan 

In order to take a Flash Loan. A prospective caller must send a `FlashLoan` variant of `ExecuteMsg` to the UST Vault. See [Flash Loan schema](../../Smart-Contracts/Stablecoin-Vault.md#flashloan) for an example JSON you can use as well as detailed info about each of the fields required in the request.

Any request to a flash loan enabled vault requires that the requested asset be the same as the base asset of the Vault. It is not possible to borrow EUT from the UST Vault. 

#### FlashLoanPayload
The FlashLoanPayload is the structure through which a user requests a flash loan. It has two fields a `requested_asset` which is of type `Asset` and a `callback` field which is a Binary. 

The `callback` Binary is where you as the loan caller can place your actions to be performed with the loaned funds. There is no restriction here whatsoever on what can be done apart from that it must be achievable within a block with some degree of profit.

The `callback` Binary is passed straight to the composition of a new CosmosMsg which will be encapsulated within the Vaults profitability logic:

```rust
// Construct return call with received binary
let return_call = CosmosMsg::Wasm(WasmMsg::Execute {
    contract_addr: info.sender.into(),
    msg: payload.callback,
    funds: vec![],
});
```

A key point to take in here is that we do no checking on what your callback series of messages should do after getting funding, that is up to you. This includes ensuring your logic works as intended but if you compose a flash loan which loses money you have some assurance in the contract's in-built profit checking logic. 

### Completing the Flash Loan 
On other chains depending on the type of flash loan an actual repayment message needs to be supplied. This makes the composability of flash loans a little harder as you are making this big sandwich of messages yourself with binaries and addresses. 

The White Whale Flash Loan approach is a little different in that we only have 1 type of Flash Loan and we handle all the repayment logic for you. This means you the user can focus on your use case for the loaned funds rather than losing hours and possible opportunities trying to figure out how to repay the thing.

### Helpful resources

In addition to this doc, the White Whale team have provided an example repo, containing a starter smart contract which uses the flash loan capability of the Vault. This starter contract provides a quick getting-started point if you just want to focus on your use case and what should happen in between the loan and payback to generate a profit. 

In future we will update this repository with more of our internal and partner created flash loan use cases either in the form of just a contract or providing both a contract and a bot to call it. 


## Going deeper on flash loans 
All of the above is nice and all but its still remains rather high level. To truly understand how the flash loan functionality we have developed works lets take a closer look at the code of the v1 release.

Starting with the flash loan call itself. Assuming we have provided a valid FlashLoanPayload the `handle_flashloan` execute message handler will first ensure the request asset is valid for the vault. 

```rust
    // Check if requested asset is base token of vault
    deposit_info.assert(&payload.requested_asset.info)?;
```

This ensures only UST can be requested from the UST Vault and Luna from the Luna Vault.

A whitelisting system is in place to restrict who can take a flash loan to only whitelisted addresses or all addresses. As of [Governance Proposal No. 4](https://app.whitewhale.money/gov/poll/4) this system was disabled and Flash Loans were officially opened too all on Terra.

Provided there is enough funds in the vault for the requested loan and a simple tax buffer the loan can proceed. Otherwise a `Broke` exception is returned:

```rust
if total_value < requested_asset.amount + tax_buffer {
        return Err(StableVaultError::Broke {});
}
```

Another check is performed to ensure the Vault has enough liquid assets to service this loan, if not a withdrawal from anchor is prepared: 

```rust
// Withdraw funds from Anchor if needed
// FEE_BUFFER as buffer for fees and taxes
if (requested_asset.amount + tax_buffer) > stables_available {
    // Attempt to remove some money from anchor
    let to_withdraw = (requested_asset.amount + tax_buffer) - stables_available;
    let aust_exchange_rate = query_aust_exchange_rate(
        env.clone(),
        deps.as_ref(),
        state.anchor_money_market_address.to_string(),
    )?;

    let withdraw_msg = anchor_withdraw_msg(
        state.aust_address,
        state.anchor_money_market_address,
        to_withdraw * aust_exchange_rate.inv().unwrap(),
    )?;
    // Add msg to response and update withdrawn value
    response = response
        .add_message(withdraw_msg)
        .add_attribute("Anchor withdrawal", to_withdraw.to_string())
        .add_attribute("ust_aust_rate", aust_exchange_rate.to_string());
}
```

Assuming all is well with the above checks and balances, we now have funds ready to service a loan. The desired action messages to perform are passed to the [encapsulate_payload](https://github.com/White-Whale-Defi-Platform/contracts/blob/4f7d90b3bcb81c4c78de07c92fa2dd11322aff02/contracts/stablecoin-vault/src/contract.rs#L591)  function which wraps up all the messages as submessages also adding the `AfterTrade` CallbackMsg to the end as a message. This CallBackMsg is what handles the final repayment of the flash loan and also calculates the final profit of the loan. 

> Note: When calculating the final profit and performance of the loan. If a loss is detected, which can be described as any non-complete repayment of both the loan and the loan fee then the entire transaction will be reverted as a losing trade

### Conclusion 

The above is a whole lot to digest but it is provided in the hopes Flash Loans can be somewhat demystified in DeFi and as we continue to advance in the space they may become a tool everyone can use. 

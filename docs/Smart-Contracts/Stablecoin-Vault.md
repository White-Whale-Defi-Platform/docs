# Stablecoin Vault

The Stablecoin Vault Contract is one of the flagship contracts in the White Whale system. It combined with a bot system allows for the automated trading of UST in response to a de-pegging event or to just perform a simple arbitrage operation. The Stablecoin Vault allows users to deposit just UST to then be exposed to arbitrage operations.

## Config

| Key | Type | Description |
| :--- | :--- | :--- |
| `trader` | CanonicalAddr | Address of the assigned trader for this vault |
| `pool_address` | CanonicalAddr | Contract address of TerraSwap pool that will be interacted with for trades|
| `anchor_money_market_address` | CanonicalAddr | Contract address of Anchor Money Market system. This is used for depositing and withdrawing from Anchor |
| `aust_address` | CanonicalAddr | Contract address of aUST token |
| `seignorage_address` | CanonicalAddr | Contract address of Seignorage system. This is used for burning/minting as a part of L1 arbitrage |
| `profit_check_address` | CanonicalAddr | Contract address of Profit Check Contract. |
| `anchor_min_withdraw_amount` | Uint128 | Minimum UST withdraw required to trigger a withdrawal from Anchor Money Market |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub pool_address: String,
    pub anchor_money_market_address: String,
    pub aust_address: String,
    pub seignorage_address: String,
    pub profit_check_address: String,
    pub community_fund_addr: String,
    pub warchest_addr: String,
    pub asset_info: AssetInfo,
    pub slippage: Decimal,
    pub token_code_id: u64,
    pub warchest_fee: Decimal,
    pub community_fund_fee: Decimal,
    pub max_community_fund_fee: Uint128,
    pub anchor_min_withdraw_amount: Uint128
}
```

```javascript
{
    "pool_address": '<UST Address>',
    "asset_info": {
        "native_token": { "denom": "uusd" }
    },
    "aust_address": '<aUST Address>',
    "anchor_money_market_address": '<Anchor MM Address>',
    "seignorage_address": '<Seignorage Address>',
    "profit_check_address": '<Profit Check Address>',
    "slippage": "0.01",
    "token_code_id": 148,
    "community_fund_addr": "<Community Fund Address>",
    "warchest_addr": "<Warchest Fund Address>",
    "warchest_fee": "0.001",
    "community_fund_fee": "0.005",
    "max_community_fund_fee": "1000000",
    "denom": "uusd",
    "anchor_min_withdraw_amount": "100000000"
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `pool_address` | CanonicalAddr | Contract address of TerraSwap pool that will be interacted with for trades  d|
| `anchor_money_market_address` | CanonicalAddr | Contract address of Anchor Money Market system. This is used for depositing and withdrawing from Anchor |
| `aust_address` | CanonicalAddr | Contract address of aUST token |
| `seignorage_address` | CanonicalAddr | Contract address of Seignorage system. This is used for burning/minting as a part of L1 arbitrage |
| `profit_check_address` | CanonicalAddr | Contract address of Profit Check Contract |
| `warchest_address` | CanonicalAddr | Contract address of Profit Check Contract |
| `asset_info` | AssetInfo | Struct detailing the token to be used for trading |
| `slippage` | Decimal | Slippage rate regarded as acceptable. |
| `token_code_id` | u64 | The Stored Code Object ID for the LP token creator. This is used on instantiation to creation an LP token when the Vault is created |
| `warchest_fee` | Decimal | Configurable fee rate for the warchest contract. |
| `community_fund_fee` | Decimal | Configurable fee rate for the community fund |
| `max_community_fund_fee` | Uint128 | Absolute max rate for community fund |
| `anchor_min_withdraw_amount` | Uint128 | Minimum UST withdraw required to trigger a withdrawal from Anchor Money Market |

## ExecuteMsg

### `Receive`

Can be called during a CW20 token transfer when tokens are deposited into the Stablecoin Vault. Allows the token transfer to execute a [Receive Hook](Stablecoin-Vault.md#receive-hooks) as a subsequent action within the same transaction.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum HandleMsg {
    Receive {
        amount: Uint128,
        sender: HumanAddr,
        msg: Option<Binary>,
    }
}
```

```javascript
{
  "receive": {
    "amount": "10000000",
    "sender": "terra1...",
    "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9"
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Uint128 | Amount of tokens received |
| `sender` | HumanAddr | Sender of token transfer |
| `msg`\* | Binary | Base64-encoded JSON of [Receive Hook](Stablecoin-Vault.md#receive-hooks) |

\* = optional

### AbovePeg

Attempt to perform an arbitrage operation with the assumption that the token to be arb'd is above peg

This is important as checks are performed to ensure the arb opportunity still exists and price is indeed above peg.

If needed, funds are withdrawn from anchor and messages are prepared to perform the swaps. Before sending; two Profit Check contract messages are also added by providing the `swapmsg` and `terraswap` msg to `add_profit_check` function.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AbovePeg {
        amount: Coin,
        uaust_withdraw_amount: Uint128,
    }
}
```

```javascript
{
  "receive": {
    "amount": "10000000",
    "uaust_withdraw_amount": "5000000",
  }
}
```

### BelowPeg

Attempt to perform an arbitrage operation with the assumption that the token to be arb'd is below it's peg.

This is important as checks are performed to ensure the arb opportunity still exists and price is indeed below it's peg.

If needed, funds are withdrawn from anchor and messages are prepared to perform the swaps. Before sending; two Profit Check contract messages are also added by providing the `swapmsg` and `terraswap` msg to `add_profit_check` function.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    BelowPeg {
        amount: Coin,
        uaust_withdraw_amount: Uint128,
    }
}
```

```javascript
{
  "receive": {
    "amount": "10000000",
    "uaust_withdraw_amount": "5000000",
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Coin | Amount of tokens to trade |
| `uaust_withdraw_amount` | Uint128 | If this value is non-zero, it represents the amount of money which the contract will attempt to withdraw from anchor to execute the arbitrage trade |

### ProvideLiquidity

Attempt to perform a deposit into the vault by providing UST liquidity. Protocol fees for the White Whale platform are calculated and subtracted from the deposit automatically. Fees are then distributed accordingly to the Community Fund.
In the event of a successful deposit, the address will receive back newly minted LP tokens representing their share of the Vault's liquidity. These LP tokens are then needed when an account wishes to withdraw liquidity.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ProvideLiquidity {
        asset: Asset
    }
}
```

```javascript
{
  "provide_liquidity": {
    "asset": {
        "info": {
            "native_token": { "denom": "uusd" }
        },
        "amount": "10000000"
    }
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `asset` | Asset | Asset to be provided as liquidity. Includes asset info and amount. |

### AnchorDeposit

Trigger the Vault to try and perform a deposit of the specified amount of `Coin` into Anchor Money Market.

Checks are performed to ensure the `Coin` is UST and then the funds are deposited to Anchor.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AnchorDeposit {
        amount: Coin
    }
}
```

```javascript
{
  "provide_liquidity": {
    "anchor_deposit": {
         "amount": {
             "denom": "uusd",
             "amount": "10000000"
         }
    }
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Coin | Coin to be deposited into Anchor. Includes asset denom and amount. Denom must be `uusd` but this check may be removed in other vaults |

### SetSlippage

Change the slippage parameter for the Stablecoin Vault which represents the maximum allowed slippage. Can only be called by the established Admin of the contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetSlippage {
        slippage: Decimal
    }
}
```

```javascript
{
  "set_slipage": {
    "slippage": "0.005"
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `slippage` | Decimal | New slippage value to be set. |

### SetAdmin

Change the established Admin for the Stablecoin Vault. Can only be called by the currently established Admin of the contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetAdmin {
        admin: Addr
    }
}
```

```javascript
{
  "set_admin": {
    "admin": "terra1..."
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `admin` | Addr | Address of the new Admin. |

### SetTrader

Update the address of the nominated trader. Can only be called by Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetTrader {
        trader: Addr
    }
}
```

```javascript
{
  "set_trader": {
    "trader": "terra1..."
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `trader` | Addr | Address of the new Trader. |

### SetFee

Update the fee information for 1 or more of the associated fee structures. Can only be called by Admin

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetFee{
        community_fund_fee: Option<CappedFee>,
        warchest_fee: Option<Fee>,
    }
}
```

```javascript
{
  "set_fee": {
    "community_fund_fee": {
      "max_fee": "10000000",
      "fee": {
        "share": "0.01"
      }
    }
    "warchest_fee": {
      "fee":{
        "share": "0.01"
      }
    }
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `community_fund_fee`\* | CappedFee | New fee values for fee allocation to the Community Fund |
| `warchest_fee`\* | CappedFee | New fee values for fee allocation to the Warchest |

\* = optional

## Receive Hooks

TODO: Receive Hooks

## QueryMsg


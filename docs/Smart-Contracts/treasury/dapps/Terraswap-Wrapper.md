# Terraswap

Wrapper contract which implements a method to interact with a liquidity pool. The contract can be used as the basis to implement protocol owned liquidity

## State

| Key | Type | Description |
| :--- | :--- | :--- |
| `terraswap_pool_addr` | Addr | Address of the Terraswap Liquidity Pool to interact with. |
| `trader` | HumanAddr | Address of the nominated trader. |
| `max_deposit` | Asset | Information representing the minimum expected profit and in what asset it is tracked. |
| `min_profit` | Asset | Information representing the minimum expected profit and in what asset it is tracked. |
| `slippage` | Decimal | Maximum allowed slippage |

## InitMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub terraswap_pool_addr: String,
    pub trader: String,
    pub max_deposit: Asset,
    pub min_profit: Asset,
    pub slippage: Decimal,
}
```

```javascript
{
  "terraswap_pool_addr": "terra1...", 
  "trader": "terra1...", 
  "max_deposit": {
      "info": "<AssetInfo>",
      "amount": "100000"
  },
  "min_profit": {
      "info": "<AssetInfo>",
      "amount": "100000"
  },
  "slippage": "0.01"
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `terraswap_pool_addr` | Addr | Address of the Terraswap Liquidity Pool to interact with. |
| `trader` | HumanAddr | Address of the nominated trader. |
| `max_deposit` | Asset | Information representing the minimum expected profit and in what asset it is tracked.d |
| `min_profit` | Asset | Information representing the minimum expected profit and in what asset it is tracked. |
| `slippage` | Decimal | Maximum allowed slippage |

## ExecuteMsg

### `Deposit`

Deposit one or more assets into the liquidity pool and receive LP tokens in return.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Deposit {
        funds: Vec<Asset>
    }
}
```

```javascript
{
  "deposit": {
    "funds": [{
      "info": "<AssetInfo>",
      "amount": "100000"
    }]
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `funds` | Vec of Assets | Vector containing a list of assets to be deposited. |

### `Withdraw`

Withdraw liquidity from the pool. Can only be called by the nominated trader.
Ideally should be used along with `WithdrawableProfits` query.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Withdraw {
        funds: Vec<Asset>
    }
}
```

```javascript
{
  "withdraw": {
    "funds": [{
      "info": "<AssetInfo>",
      "amount": "100000"
    }]
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `funds` | Vec of Assets | Vector containing a list of assets to be provided for withdraw. |

### SetTrader

Updates the address of the nominated trader. Can only be called by Admin.

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

### SetMaxDeposit

Updates the configured Max Deposit value. Can only be called by Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetMaxDeposit {
        asset: Asset
    }
}
```

```javascript
{
  "set_max_deposit": {
    "asset": {
      "info": "<AssetInfo>",
      "amount": "100000"
    }
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `asset` | Asset | Asset info for the new Max Deposit value. |

### SetMinDeposit

Updates the configured Min Profit value. Can only be called by Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetMinProfit {
        asset: Asset
    }
}
```

```javascript
{
  "set_min_profit": {
    "asset": {
      "info": "<AssetInfo>",
      "amount": "100000"
    }
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `asset` | Asset | Asset info for the new Min Profit value. |

### Spend

Allows for the transfer of assets to the specified address. Can only be called by Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Spend {
        recipient: String,
        amount: Asset
    }
}
```

```javascript
{
  "spend": {
    "recipient": "terra1..."
    "amount": {
      "info": "<AssetInfo>",
      "amount": "100000"
    }
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `recipient` | String | Address of the recipient to send funds to. |
| `amount` | Asset | Asset info for the amount of tokens to be spent. |

## QueryMsg

### `WithdrawableProfits`

Query to get the withdrawable profits for the wrapper contract. Withdrawable profit is defined as the value of the holdings - max_deposit in either the asset denom(X) or the base denom(UST)

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    WithdrawableProfits {},
}
```

```javascript
{
  "withdrawable_profits": {} 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `WithdrawableProfitsResponse`

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct WithdrawableProfitsResponse {
    pub amount: Asset,
    pub lp_amount: Uint128,
}
```

```javascript
{
  "lp_amount": "100000000", 
  "amount": {
      "info": "<AssetInfo>",
      "amount": "100000"
    }, 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `lp_amount` | Uint128 | Amount of LP token withdrawn |
| `amount` | Asset | Asset info for the withdrawn assets |

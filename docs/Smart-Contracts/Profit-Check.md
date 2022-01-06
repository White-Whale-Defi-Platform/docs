# Profit Check

Profit check is used by the ust vault to see if a proposed trade is indeed profitable. `before_trade` is called before the trade to set the account balance and `after_trade` is called after the trade and checks whether a profit was made
If the balance of the contract is smaller after the trade, a `CancelLosingTrade` error gets thrown which resets the contract state to the state before the contract call.
If a profit is indeed made, it calls the vault to send a portion of the profit to the treasury contract as commission.

## Config

| Key | Type | Description |
| :--- | :--- | :--- |
| `vault_address` | String | Address of the arb vault which will be interfaced with |
| `denom` | String | Denom of the token to be checked on profit check operations |
| `last_balance` | Uint128 | State value used during profit checking operations |
| `last_profit` | Uint128 | State value used during profit checking operations |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub vault_address: String,
    pub denom: String,
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `vault_address` | String | Address of the arb vault which will be interfaced with |
| `denom` | String | Denom of the token to be checked on profit check operations |

## ExecuteMsg

### AfterTrade

Checks if the balance increased after the trade. When called, the current balance of the vault is queried and if the balance is less than the last balance value which is retrieved from stored state, then a `CancelLosingTrade` error is raised.

AfterTrade can only be called by the registered vault contract stored in State as `vault_address`.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AfterTrade {},
}
```

### BeforeTrade

Resets last trade and sets current UST balance of caller. Simply put, prepares the contracts state to record the next AfterTrade result.

BeforeTrade can only be called by the registered vault contract stored in State as `vault_address`.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    BeforeTrade {},
}
```

### SetVault

Change the registered Vault contract address for the Profit Check contract. Can only be called by the Admin of the contract.

The registered `vault_address` is the only address able to call the Profit Check contracts `BeforeTrade` and `AfterTrade` functions.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetVault {
        vault_address: Addr
    }
}
```

## QueryMsg

### `LastBalance`

Gets the `last_balance` state value. This value is updated every time `BeforeTrade` is called.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    LastBalance {} 
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `LastBalanceResponse`

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct LastBalanceResponse {
    pub last_balance: Uint128,
}
```



| Key | Type | Description |
| :--- | :--- | :--- |
| `last_balance` | Uint128 | Last recorded balance of the registered vault address. |

### `LastProfit`

Gets the `last_profit` state value. This value is reset every time `BeforeTrade` is called.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    LastProfit {} 
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `LastProfitResponse`

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct LastProfitResponse {
    pub last_profit: Uint128,
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
| `last_profit` | Uint128 | Last recorded profit of the registered vault address. |

### `Vault`

Gets the `vault_address` state value which represents the currently registered Vault contract address.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Vault {} 
}
```



| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `VaultResponse`

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct VaultResponse {
    pub vault_address: Uint128,
}
```



| Key | Type | Description |
| :--- | :--- | :--- |
| `vault_address` | Addr | Contract address of the Vault contract that is registered with this Profit Check contract. |


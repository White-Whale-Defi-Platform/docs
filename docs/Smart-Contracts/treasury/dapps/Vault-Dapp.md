# Vault Dapp


## Config

This dapps config is composed of two state objects. A `BaseState` which every dapp have and an optionally used `State` for any Dapp-specific config values 

### BaseState

See [Dapp Base](Dapp-Base.md)

### State

| Name | Type | Description |
| :--- | :--- | :--- |
| `liquidity_token` | Addr | The contract address of the UST-Vault's LP token |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub base: BaseInstantiateMsg,
    pub token_code_id: u64,
    pub fee: Decimal,
    pub deposit_asset: String,
    pub vault_lp_token_name: Option<String>,
    pub vault_lp_token_symbol: Option<String>,
}
```

## ExecuteMsg

Available Execution operations on the Vault-Dapp: 

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub enum ExecuteMsg {
    Base(BaseExecuteMsg),
    // Add dapp-specific messages here
    Receive(Cw20ReceiveMsg),
    ProvideLiquidity {
        asset: Asset,
    },
    UpdatePool {
        deposit_asset: Option<String>,
        assets_to_add: Vec<String>,
        assets_to_remove: Vec<String>,
    },
    SetFee {
        fee: Fee,
    },
}
```

### `Base`

See `handle_base_message` in [Dapp Base](../Dapp-Base/#handle_base_message)


### `Receive`

Can be called during a CW20 token transfer when tokens are deposited into the Stablecoin Vault. Allows the token transfer to execute a [Receive Hook](Stablecoin-Vault.md#receive-hooks) as a subsequent action within the same transaction.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum Receive {
    Cw20ReceiveMsg {
        amount: Uint128,
        sender: HumanAddr,
        msg: Option<Binary>,
    }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Uint128 | Amount of tokens received |
| `sender` | HumanAddr | Sender of token transfer |
| `msg`\* | Binary | Base64-encoded JSON of [Receive Hook](Stablecoin-Vault.md#receive-hooks) |

\* = optional

### `ProvideLiquidity`

An abstraction around liquidity provision allowing a vault to have liquidity provided to it either with a Native token or with a CW20. 

| Key | Type | Description |
| :--- | :--- | :--- |
| `asset` | Asset | Asset to be provided as liquidity. Includes asset info and amount. |


```json 
{
    "provide_liquidity": {
        "asset": {
            "info": {
                "native_token": { "denom": "uusd" },
            },
            "amount": "1000000",
        },
}
```

### `UpdatePool`

Update the vault's associated pool information and asset information.

| Key | Type | Description |
| :--- | :--- | :--- |
| `deposit_asset`\* | Option<String> | New deposit asset to be used for the pool. |
| `assets_to_add`\* | Vec<String> | Assets to be included as claimable |
| `assets_to_remove`\* | Vec<String> | Assets to be removed from being claimable |

> Note: The 'assets' defined in this call use reference names which are stored in the memory contract rather than full asset information or contract addresses. See the Memory Contract for more

```json 
{
    "update_pool": {
        "deposit_asset": "ust",
        "assets_to_add": ["ust", "krw", "anotherone"],
        "assets_to_remove": ["bluna"]
    }
}
        
```

### `SetFee`

Update the configured fee information for the vault-dapp. 

| Key | Type | Description |
| :--- | :--- | :--- |
| `fee`\* | Fee | New fee information to set. |

```json 
{
    "set_fee": {
        "fee": {
            "share": 1.01
        }
    }
}
```

## Receive Hooks

Payable functions when you send a payment to the contract with an appropriate message.

### `WithdrawLiquidity`

Attempt to withdraw deposits. Fees are calculated and deducted. LP tokens submitted with a withdrawal request are burned. The Treasury withdraw fee is paid out by transfering ownership of a fraction of the LP tokens to the treasury contract. 

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum Cw20HookMsg {
    WithdrawLiquidity {},
}
```

## QueryMsg

```rust
pub enum QueryMsg {
    Base(BaseQueryMsg),
    // Add dapp-specific queries here
    State {},
}
```

### `Base`

See `handle_base_query` in [Dapp Base](../Dapp-Base/#handle_base_query)

###  `State`

Returns the saved dapp-specific configuration values saved in State.

```javascript
{
  "state": {}
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `StateResponse`

Saved dapp-specific configuration values. 

```javascript
{
  "liquidity_token": "terra1..."
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `liquidity_token` | String | The contract address of the Vault's LP token |

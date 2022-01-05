# Vault Dapp


## Config

This dapps config is composed of two state objects. A `BaseState` which every dapp have and an optionally used `State` for any Dapp-specific config values 

### BaseState

See dapp base

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
pub fn execute(deps: DepsMut, env: Env, info: MessageInfo, msg: ExecuteMsg) -> VaultResult {
    match msg {
        ExecuteMsg::Base(message) => {
            from_base_dapp_result(dapp_base_commands::handle_base_message(deps, info, message))
        }
        ExecuteMsg::Receive(msg) => commands::receive_cw20(deps, env, info, msg),
        ExecuteMsg::ProvideLiquidity { asset } => {
            commands::try_provide_liquidity(deps, info, asset, None)
        }
        ExecuteMsg::UpdatePool {
            deposit_asset,
            assets_to_add,
            assets_to_remove,
        } => commands::update_pool(deps, info, deposit_asset, assets_to_add, assets_to_remove),
        ExecuteMsg::SetFee { fee } => commands::set_fee(deps, info, fee),
    }
}
```

### `Base`

See dapp-base

### `Receive`

Can be called during a CW20 token transfer when tokens are deposited into the Stablecoin Vault. Allows the token transfer to execute a [Receive Hook](Stablecoin-Vault.md#receive-hooks) as a subsequent action within the same transaction.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Receive {
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
| `sender`\* | Option<String> | Optionally, specify the provider of liquidity as a seperate address. Minted LP tokens will be sent to this address |

\* = optional

```json 
{
    "provide_liquidity": {
        "asset": {
            "info": {
                "native_token": { "denom": "uusd" },
            },
            "amount": "1000000",
        },
        "sender": "terra1...."
}
```

### `UpdatePool`

Update the vault's associated pool information and asset information.

| Key | Type | Description |
| :--- | :--- | :--- |
| `deposit_asset`\* | Option<String> | New deposit asset to be used for the pool. |
| `assets_to_add`\* | Vec<String> | A number of Assets to be to the pool |
| `assets_to_remove`\* | Vec<String> | A number of Assets to be to the pool |

> Note: The 'assets' defined in this call use reference names which are stored in the memory contract rather than full asset information or contract addresses. See the Memory Contract for more

```json 
{
    "deposit_asset": "ust",
    "assets_to_add": ["ust", "krw", "anotherone"],
    "assets_to_add": ["eut"]
}
        
```

### `SetFee`

Update the configured fee information for the vault-dapp. 

| Key | Type | Description |
| :--- | :--- | :--- |
| `new_fee`\* | Fee | New fee information to set. |

```json 
{
    "new_fee": {
        "share": 1.01
    }
}
```

## Receive Hooks

Payable functions when you send a payment to the contract with an appropriate message.

### `WithdrawLiquidity`

Attempt to withdraw deposits. Fees are calculated and deducted. If the Vault has available funds in Anchor it will attempt to payout the withdrawal request by first withdrawing some funds from Anchor to fund the withdrawal request. Luna holdings are not eligible for withdrawal. LP tokens submitted with a withdrawal request will be burned. The Warchest withdraw fee is paid out by transfering ownership of a fraction of the LP tokens to the warchest contract. 

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum Cw20HookMsg {
    WithdrawLiquidity {},
}
```

## QueryMsg

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> StdResult<Binary> {
    match msg {
        QueryMsg::Base(message) => dapp_base_queries::handle_base_query(deps, message),
        // handle dapp-specific queries here
        QueryMsg::State {} => to_binary(&StateResponse {
            liquidity_token: STATE.load(deps.storage)?.liquidity_token_addr.to_string(),
        }),
        QueryMsg::ValueQuery(query) => to_binary(&STATE.load(deps.storage)?), //queries::handle_value_query(deps, query),
    }
}
```

### `Base`

See dapp-base

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
| `liquidity_token` | Addr | The contract address of the UST-Vault's LP token |


### `VaultQuery`

Perform a value query on the Vault. 

```javascript
{
  "value": "...", 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |
# Anchor Dapp


## Config

This dapps config is composed of two state objects. A `BaseState` which every dapp have and an optionally used `State` for any Dapp-specific config values 

### BaseState

See [Dapp Base](Dapp-Base.md)

### State

| Name | Type | Description |
| :--- | :--- | :--- |
| | | |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub base: BaseInstantiateMsg,
}
```

## ExecuteMsg

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn execute(deps: DepsMut, env: Env, info: MessageInfo, msg: ExecuteMsg) -> BaseDAppResult {
    match msg {
        ExecuteMsg::Base(message) => dapp_base_commands::handle_base_message(deps, info, message),
        // handle dapp-specific messages here
        // ExecuteMsg::Custom{} => commands::custom_command(),
        ExecuteMsg::DepositStable { deposit_amount } => {
            commands::handle_deposit_stable(deps.as_ref(), env, info, deposit_amount)
        }
        ExecuteMsg::RedeemStable { withdraw_amount } => {
            commands::handle_redeem_stable(deps.as_ref(), env, info, withdraw_amount)
        }
    }
}
```

### `DepositStable`

Attempt to deposit an amount of UST into Anchor on behalf of the Treasury. 

| Key | Type | Description |
| :--- | :--- | :--- |
| `ust_deposit_amount` | Uint128 | Amount of UST to be deposited into Anchor. |

```json 
{
    "deposit_stable": {
        "ust_deposit_amount": "1000..."
}
```

### `RedeemStable`

Attempt to withdraw an amount of UST into Anchor on behalf of the Treasury. 

| Key | Type | Description |
| :--- | :--- | :--- |
| `ust_to_withdraw` | Uint128 | Amount of UST to be withdrawn from Anchor. |

```json 
{
    "redeem_stable": {
        "ust_to_withdraw": "1000..."
}
```

## QueryMsg 

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> StdResult<Binary> {
    match msg {
        QueryMsg::Base(message) => dapp_base_queries::handle_base_query(deps, message),
        // handle dapp-specific queries here
        // QueryMsg::Custom{} => queries::custom_query(),
    }
}
```
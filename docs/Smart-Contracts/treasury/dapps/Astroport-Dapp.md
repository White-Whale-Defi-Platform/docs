# Astroport Dapp

## Config 

### BaseState

See [Dapp BaseState](../Dapp-Base/#basestate)

## InstantiateMsg

See [Dapp BaseInstantiateMsg](../Dapp-Base/#instantiatemsg)


## ExecuteMsg 

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn execute(deps: DepsMut, env: Env, info: MessageInfo, msg: ExecuteMsg) -> AstroportResult {
    match msg {
        ExecuteMsg::ProvideLiquidity {
            pool_id,
            main_asset_id,
            amount,
        } => commands::provide_liquidity(deps.as_ref(), info, main_asset_id, pool_id, amount),
        ExecuteMsg::DetailedProvideLiquidity {
            pool_id,
            assets,
            slippage_tolerance,
        } => commands::detailed_provide_liquidity(
            deps.as_ref(),
            info,
            assets,
            pool_id,
            slippage_tolerance,
        ),
        ExecuteMsg::WithdrawLiquidity {
            lp_token_id,
            amount,
        } => commands::withdraw_liquidity(deps.as_ref(), info, lp_token_id, amount),
        ExecuteMsg::SwapAsset {
            offer_id,
            pool_id,
            amount,
            max_spread,
            belief_price,
        } => commands::astroport_swap(
            deps.as_ref(),
            env,
            info,
            offer_id,
            pool_id,
            amount,
            max_spread,
            belief_price,
        ),
        ExecuteMsg::Base(message) => {
            from_base_dapp_result(dapp_base_commands::handle_base_message(deps, info, message))
        }
    }
}
```


### `Base`

See `handle_base_message` in [Dapp Base](../Dapp-Base/#handle_base_message)

### `ProvideLiquidity`

An abstraction around liquidity provision for Terraswap. Rather than specifying the contract addresses and asset structures, the caller provides a referential ID for the relevant contract addresses which have been stored in the contracts associated `Memory` contract. After the message is composed it is sent to the treasury for execution. The contract fetches what other asset is paired with the main_asset in the provided pool and adds that to the pool at the current ratio of the pool.

| Key | Type | Description |
| :--- | :--- | :--- |
| `pool_id` | String | The reference name for the stored pool contract. Every trading pair ends with "_pair"|
| `main_asset_id` | String | The reference name for the stored Asset to be used as the 'main' Asset |
| `amount` | UInt128 | The amount of the specified main asset to be added to the pool |

```json 
{
    "provide_liquidity": {
        "pool_id": "whale_ust_pair",
        "main_asset_id": "ust",
        "amount": "10000"
}
```

### `DetailedProvideLiquidity`

Similar to `ProvideLiquidity` but with the ability to specify both asset amounts to be added and with what amount of slippage. Rather than specifying the contract addresses and asset structures, the caller provides a referential ID for the relevant contract addresses which have been stored in the contracts associated `Memory` contract. After the message is composed it is sent to the treasury for execution.

| Key | Type | Description |
| :--- | :--- | :--- |
| `pool_id` | String | The reference name for the stored pool contract. |
| `assets` | Vec[String] | Vector containing 2 asset reference names. |
| `slippage_tolerance` | Option[Decimal] | The maximum amount of slippage considered acceptable. |

```json 
{
    "detailed_provide_liquidity": {
        "pool_id": "whale_ust",
        "assets": ["stored_ust", "stored_krt"],
        "slippage_tolerance": null
}
```

### `WithdrawLiquidity`

Composes a message to withdraw liquidity by returning an amount of the LP token received on liquidity provision. The message is then sent to the treasury for execution.

| Key | Type | Description |
| :--- | :--- | :--- |
| `lp_token_id` | String | The reference name for the stored liquidity token contract. |
| `amount` | UInt128 | The amount of lp tokens to be sent |


```json 
{
    "withdraw_liquidity": {
        "lp_token_id": "whale_ust",
        "amount": "100000",
}
```

### `SwapAsset`

ExecuteMsg used to compose a swap message for Terraswap which is then send to the Treasury for execution.

| Key | Type | Description |
| :--- | :--- | :--- |
| `offer_id` | String | The reference name for the offered Asset's info. |
| `pool_id` | String | The reference name for the stored pool contract. |
| `amount` | UInt128 | The offer asset amount |
| `max_spread` | Option<Decimal> | The maximum amount of spread considered acceptable. |
| `belief_price` | Option<Decimal> | The current user-provided price for the token. When provided, `max_spread` is used to determine whether the trade is acceptable. |

```json 
{
    "swap_asset": {
        "offer_id": "my_offer",
        "pool_id": "whale_ust",
        "amount": "100000",
        "max_spread": null,
        "slippage_tolerance": null
}
```

## QueryMsg

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> StdResult<Binary> {
    match msg {
        QueryMsg::Base(message) => dapp_base_queries::handle_base_query(deps, message),
    }
}
```

### `Base`

See `handle_base_query` in [Dapp Base](../Dapp-Base/#handle_base_query)
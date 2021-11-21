# LP Emissions Proxy Contract

This proxy contract handles staking the provided LP tokens to our own LP emissions contract while allowing for dual incetives from Astroport. A detailed explanation can be found [here](https://astroport.medium.com/project-guide-how-to-integrate-dual-incentives-with-astroport-generators-2aa13169c5c1)

## Config

| Key | Type | Description |
| :--- | :--- | :--- |
| `generator_contract_addr` | Addr | Address of generator contract |
| `pair_addr` | Addr | Address of the WHALE/UST pair |
| `lp_token_addr` | Addr | Address of the WHALE/UST LP token |
| `reward_contract_addr` | Addr | Address of the LP emissions contract |
| `reward_token_addr` | Addr | Whale token address |


## Messages
## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub vault_address: String,
    pub seignorage_address: String,
    pub pool_address: String,
    pub asset_info: AssetInfo,
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
| `vault_address` | String | Contract address of Anchor Money Market system. This is used for depositing and withdrawing from Anchor |
| `seignorage_address` | String | Contract address of aUST token |
| `pool_address` | String | Contract address of Profit Check Contract |
| `warchest_address` | String | Contract address of Profit Check Contract |
| `asset_info` | AssetInfo | Struct detailing the token to be used for trading (the vault base token) |


## ExecuteMsg

### `ExecuteArb`

Endpoint for the arbitrage bot to call. The arb bot provides the arb details and direction to the contract. These details are determined by the arbitrage bots.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum HandleMsg {
    ExecuteArb {
        details: ArbDetails,
        above_peg: bool,
    }
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ArbDetails {
    pub asset: Asset,
    pub slippage: Decimal,
    pub belief_price: Decimal,
}

```



| Key | Type | Description |
| :--- | :--- | :--- |
| `details` | ArbDetails | Conveys the amount, max allowed slippage and belief price |
| `above_peg` | bool | Conveys if UST is below or above peg |

### AbovePegCallback

After the stablecoin vault transfered the requested funds to this contract it will call the provided callback message. The AbovePegCallback and BelowPegCallback messages are both callback endpoints for the stablecoin vault. This endpoint can only be call by the vault.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AbovePegCallback {
        details: ArbDetails,
    },
}

```

```javascript WIP
{
  
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `details` | ArbDetails | Conveys the amount, max allowed slippage and belief price |

### BelowPegCallback

After the stablecoin vault transfered the requested funds to this contract it will call the provided callback message. The AbovePegCallback and BelowPegCallback messages are both callback endpoints for the stablecoin vault. This endpoint can only be call by the vault.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AbovePegCallback {
        details: ArbDetails,
    },
}

```

```javascript WIP
{
  
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `details` | ArbDetails | Conveys the amount, max allowed slippage and belief price |

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



| Key | Type | Description |
| :--- | :--- | :--- |
| `admin` | Addr | Address of the new Admin. |

## Callbacks

These callbacks can only be called by the contract itself. No external address is allowed to execute these endpoints.

### `AfterSuccessfulTradeCallback`

After a successful trade, which means the trade was able to execute given the slippage constraints, it sends all the available UST back to the stablecoin vault contract. 

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum CallbackMsg {
    AfterSuccessfulTradeCallback {},
}
```



## QueryMsg

### `Config`

Gets the configuration for the arbitrage contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {} 
}
```

# Terra Stablecoin Arbitrage Contracts

The UST Arbitrage Contracts are our first step toward providing Anchor+ yields to depositors. These contracts are the endpoint for our bots and perform the UST -> LUNA -> UST transactions that stabilize the peg through minting/burning of luna.

This doc is valid for both the Terraswap and Astroport arbitrage contracts. 

## State

| Key | Type | Description |
| :--- | :--- | :--- |
| `vault_address` | CanonicalAddr | Addresses of the UST vault |
| `seignorage_address` | CanonicalAddr | Address of the seignorage swaps that are native to the Terra infrastructure |
| `pool_address` | CanonicalAddr | Address of either the terraswap or astroport LUNA/UST pair.  |

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
| `pool_address` | String | Contract address of LP Contract to be interacted with |
| `treasury_address` | String | Contract address of Treasury Contract |
| `asset_info` | AssetInfo | Struct detailing the token to be used for trading (the vault base token) |


## ExecuteMsg

### `ExecuteArb`

Endpoint for the arbitrage bot to call. The arb bot provides the arb details and direction to the contract. These details are determined by the arbitrage bots.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
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

| Key | Type | Description |
| :--- | :--- | :--- |
| `details` | ArbDetails | Conveys the amount, max allowed slippage and belief price |

### SendTreasuryCommission

Call executed by the profit check contract after a profitable trade. It forwards a portion of the profit to the treasury contract (aka war chest). The % of the profit sent as commission is set by the commission_fee. 

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SendTreasuryCommission { 
        profit: Uint128 
    },
}

```

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

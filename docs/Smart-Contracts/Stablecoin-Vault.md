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
    pub token_code_id: u64,
    pub warchest_fee: Decimal,
    pub community_fund_fee: Decimal,
    pub max_community_fund_fee: Uint128,
    pub stable_cap: Uint128,
    pub vault_lp_token_name: Option<String>,
    pub vault_lp_token_symbol: Option<String>
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
    "token_code_id": 148,
    "community_fund_addr": "<Community Fund Address>",
    "warchest_addr": "<Warchest Fund Address>",
    "warchest_fee": "0.001",
    "community_fund_fee": "0.005",
    "max_community_fund_fee": "1000000",
    "stable_cap": "100000000",
    "vault_lp_token_name": "Stable Coin Vault LP Token"
    "vault_lp_token_name": "vaultLP"
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
| `token_code_id` | u64 | The Stored Code Object ID for the LP token creator. This is used on instantiation to creation an LP token when the Vault is created |
| `warchest_fee` | Decimal | Configurable fee rate for the warchest contract. |
| `community_fund_fee` | Decimal | Configurable fee rate for the community fund |
| `max_community_fund_fee` | Uint128 | Absolute max rate for community fund |
| `stable_cap` | Uint128 | Initial UST_CAP value which represents the cap of liquid UST kept outside of Anchor |
| `vault_lp_token_name` | Uint128 | Absolute max rate for community fund |
| `vault_lp_token_symbol` | Uint128 | Absolute max rate for community fund |

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

Attempt to perform an arbitrage operation with the assumption that the currency to be arb'd is below peg.

If needed, funds are withdrawn from anchor and messages are prepared to perform the swaps. Two calls to a profit_check contract surround the trade msgs to ensure the trade only finalizes if the contract makes a profit.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AbovePeg {
        amount: Coin,
        slippage: Decimal,
        belief_price: Decimal
    }
}
```

```javascript
{
  "receive": {
    "amount": "10000000",
    "slippage": "0.005",
    "belief_price": "0.005"
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Coin | Amount of tokens to trade |
| `slippage` | Decimal | Maximum accepted slippage rate |
| `belief_price` | Decimal | The price the caller believes it should get |

### BelowPeg

Attempt to perform an arbitrage operation with the assumption that the currency to be arb'd is below peg.

If needed, funds are withdrawn from anchor and messages are prepared to perform the swaps. Two calls to a profit_check contract surround the trade msgs to ensure the trade only finalizes if the contract makes a profit.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    BelowPeg {
        amount: Coin,
        slippage: Decimal,
        belief_price: Decimal
    }
}
```

```javascript
{
  "receive": {
    "amount": "10000000",
    "slippage": "0.005",
    "belief_price": "0.005"
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Coin | Amount of tokens to trade |
| `slippage` | Decimal | Maximum accepted slippage rate |
| `belief_price` | Decimal | The price the caller believes it should get |

### ProvideLiquidity

Attempt to perform a deposit into the vault by providing UST liquidity. Protocol fees for the White Whale platform are calculated and subtracted from the deposit automatically. Fees are then distributed accordingly to the Community Fund.
In the event of a successful deposit, the address will receive back newly minted LP tokens representing their share of the Vault's liquidity. These LP tokens are then needed when an account wishes to withdraw liquidity.

This function should be called alongside a deposit of UST into the contract.

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

### SetStableCap

Change the UST_CAP parameter for the Stablecoin Vault which represents the cap of liquid UST kept in the vault. Deposits in excess of the cap will be deposited into anchor. Can only be called by the established Admin of the contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetStableCap {
        stable_cap: Uint128
    }
}
```

```javascript
{
  "set_stable_cap": {
    "stable_cap": "1000000"
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `stable_cap` | Uint128 | New UST_CAP value to be set |

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
    },
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

Payable functions when you send a payment to the contract with an appropriate message.

### `WithdrawLiquidity`

Attempt to withdraw deposits. Fees are calculated and deducted. The refund is taken out of Anchor if possible. Luna holdings are not eligible for withdrawal.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum Cw20HookMsg {
    WithdrawLiquidity {},
}
```

```javascript
{
  "withdraw_liquidity": {}
}
```

## Callbacks

Callback Functions are used in conjunction with other ExecuteMsg functions in the Stablecoin Vault.

### `AfterSuccessfulTradeCallback`

After a successful trade happens, check if the contract currently holds more than the ANCHOR_DEPOSIT_THRESHOLD. If it does, attempt to deposit most of the funds into Anchor, leaving a predefined amount behind which is called the Stable Cap or the UST_CAP.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum CallbackMsg {
    AfterSuccessfulTradeCallback {},
}
```

```javascript
{
  "after_successful_trade_callback": {}
}
```

## QueryMsg

### `Config`

Gets the configuration for the Stablecoin Vault contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {} 
}
```

```javascript
{
  "config": {} 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `PoolInfo`

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct PoolInfo {
    pub asset_infos: [AssetInfo; 3],
    pub contract_addr: Addr,
    pub liquidity_token: Addr,
    pub slippage: Decimal
}
```

```javascript
{
  "asset_info": [
      {
        "info" : {
            "token": {
                "contract_addr": "<HumanAddr>"
            }
        },
        "amount": "10"
      },
      {
        "info" : {
            "native_token": {
                "denom": "uluna"
            }
        },
        "amount": "10"
      }
    ],
  "contract_addr": "terra1...",
  "liquidity_token": "terra1...",
  "slippage": "0.005"
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `asset_info` | AssetInfo | Collection of Assets in the Pool and their information. |
| `contract_addr` | Addr | Address of the LP token contract. |
| `liquidity_token` | Addr | Address of the LP token |
| `slippage` | Decimal | Maximum accepted slippage value per trade. |

### `Pool`

Gets the pool created by the Stablecoin Vault contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Pool {} 
}
```

```javascript
{
  "pool": {} 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `PoolResponse`

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct PoolResponse {
    pub asset_infos: [AssetInfo; 3],
    pub contract_addr: Addr,
    pub liquidity_token: Addr,
    pub slippage: Decimal
}
```

```javascript
{
  "asset_info": [
      {
        "info" : {
            "token": {
                "contract_addr": "<HumanAddr>"
            }
        },
        "amount": "10"
      },
      {
        "info" : {
            "native_token": {
                "denom": "uluna"
            }
        },
        "amount": "10"
      }
    ],
  "contract_addr": "terra1...",
  "liquidity_token": "terra1...",
  "slippage": "0.005"
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `asset_info` | AssetInfo | Collection of Assets in the Pool and their information. |
| `contract_addr` | Addr | Address of the LP token contract. |
| `liquidity_token` | Addr | Address of the LP token |
| `slippage` | Decimal | Maximum accepted slippage value per trade. |

### `Fees`

Gets the fees structure set by the Stablecoin Vault contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Fees {} 
}
```

```javascript
{
  "fees": {} 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `FeeResponse`

```rust
pub struct FeeResponse {
    pub fees: VaultFee,
}
```

### `VaultFee`

```rust
#[derive(Deserialize, Serialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct VaultFee {
    pub community_fund_fee: CappedFee,
    pub warchest_fee: Fee,
    pub community_fund_addr: CanonicalAddr,
    pub warchest_addr: CanonicalAddr
}
```

```javascript
{
  "fees": {
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
    "community_fund_addr": "terra1...",
    "warchest_addr": "terra1..."
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `community_fund_fee` | CappedFee | Fee information related to Community Fund fee distribution. |
| `warchest_fee` | Fee | Fee information related to Warchest fee distribution. |
| `community_fund_addr` | CanonicalAddr | Address of the Community Fund Contract |
| `warchest_addr` | CanonicalAddr | Address of the Community Fund Contract |

### `EstimateDepositFee`

Query to provide an estimate of the Stablecoin Vault deposit fee, less tax.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    EstimateDepositFee{ amount: Uint128 }
}
```

```javascript
{
  "estimate_deposit_fee": { "amount": "100000"} 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Uint128 | Deposit amount to calculate a deposit fee for |

### `EstimateDepositFeeResponse`

```rust
pub struct EstimateDepositFeeResponse {
    pub fee: Vec<Coin>,
}
```

```javascript
{
  "fee": [
    {
      "denom": "uusd",
      "amount": "1000000"
    }
  ]
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `fee` | Vec | Vector containing fee info in the form of Coins |

### `EstimateWithdrawFee`

Query to provide an estimate of the Stablecoin Vault withdrawal fee, less tax.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    EstimateWithdrawFee{ amount: Uint128 }
}
```

```javascript
{
  "estimate_withdraw_fee": { "amount": "100000"} 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Uint128 | Deposit amount to calculate a deposit fee for |

### `EstimateWithdrawFeeResponse`

```rust
pub struct EstimateWithdrawFeeResponse {
    pub fee: Vec<Coin>,
}
```

```javascript
{
  "fee": [
    {
      "denom": "uusd",
      "amount": "1000000"
    }
  ]
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `fee` | Vec | Vector containing fee info in the form of Coins |

# Stablecoin Vault

The Stablecoin Vault Contract is one of the flagship contracts of the White Whale protocol. Combined with a bot system, the Vault contract allows for in-house automated trading of UST in response to a de-pegging event or to just perform a simple arbitrage operation. The Stablecoin Vault also allows arbitrary users to request flashloans in return for a small fee. 

## State

| Key | Type | Description |
| :--- | :--- | :--- |
| `whitelisted_contracts` | Vec<CanonicalAddr> | Addresses of whitelisted contracts |
| `allow_non_whitelisted` | bool | Boolean that indicates if non-whitelisted addresses are allowed to get a flashloan |
| `anchor_money_market_address` | CanonicalAddr | Contract address of Anchor Money Market system. This is used for depositing and withdrawing from Anchor |
| `aust_address` | CanonicalAddr | Contract address of aUST token |
| `profit_check_address` | CanonicalAddr | Contract address of Profit Check Contract. |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub anchor_money_market_address: String,
    pub aust_address: String,
    pub profit_check_address: String,
    pub warchest_addr: String,
    pub asset_info: AssetInfo,
    pub token_code_id: u64,
    pub warchest_fee: Decimal,
    pub flash_loan_fee: Decimal,
    pub stable_cap: Uint128,
    pub vault_lp_token_name: Option<String>,
    pub vault_lp_token_symbol: Option<String>,
}
```

```javascript WIP
{
    "asset_info": {
        "native_token": { "denom": "uusd" }
    },
    "aust_address": '<aUST Address>',
    "anchor_money_market_address": '<Anchor MM Address>',
    "profit_check_address": '<Profit Check Address>',
    "token_code_id": 148,
    "warchest_addr": "<Warchest Fund Address>",
    "warchest_fee": "0.001",
    "stable_cap": "100000000",
    "vault_lp_token_name": "Stable Coin Vault LP Token"
    "vault_lp_token_name": "vaultLP"
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `anchor_money_market_address` | String | Contract address of Anchor Money Market system. This is used for depositing and withdrawing from Anchor |
| `aust_address` | String | Contract address of aUST token |
| `profit_check_address` | String | Contract address of Profit Check Contract |
| `warchest_address` | String | Contract address of Profit Check Contract |
| `asset_info` | AssetInfo | Struct detailing the token to be used for trading (the vault base token) |
| `token_code_id` | u64 | The Stored Code Object ID for the LP token creator. This is used on instantiation to creation an LP token when the Vault is created |
| `warchest_fee` | Decimal | Configurable fee rate for the warchest contract. |
| `stable_cap` | Uint128 | Initial UST_CAP value which represents the amount of liquid UST kept outside of Anchor |
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

### FlashLoan

Attempt to get a flashloan from the vault. The requested funds and callback message are encapsuled in the FlashLoanPayload struct. The provided callback is required to be an ExecuteMessage on contract requesting the funds. 

If needed, funds are withdrawn from anchor. All incured fees are paid by the borrower. Two calls to the profit_check contract surround the flashloan callback msg to ensure the trade only finalizes if the contract makes a profit.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    FlashLoan {
        payload: FlashLoanPayload,
    },
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct FlashLoanPayload {
    pub requested_asset: Asset,
    pub callback: Binary,
}
```

```javascript WIP
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

Attempt to perform a deposit into the vault by providing UST. White Whale does not charge any additional fees on depositing.
In the event of a successful deposit, the address will receive newly minted LP tokens representing their share of the Vault's liquidity. These LP tokens are then required when an account wishes to withdraw liquidity.

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

### AddToWhitelist

Adds the provided address to the whitelist. Can only be called by Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AddToWhitelist {
        contracts_addr: Addr
    }
}
```

```javascript WIP
{
  "add_to_whitelist": {
    "contract_addr": "terra1..."
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `contract_addr` | Addr | Address of the new whitelisted contract. |

### RemoveFromWhitelist

Removes the provided address from the whitelist. Can only be called by Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    RemoveFromWhitelist {
        contracts_addr: Addr
    }
}
```

```javascript WIP
{
  "remove_from_whitelist": {
    "contract_addr": "terra1..."
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `contract_addr` | Addr | Address of the whitelisted contract. |

### SetFee

Update the fee information for 1 or more of the associated fee structures. Can only be called by Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetFee{
        flash_loan_fee: Option<Fee>,
        warchest_fee: Option<Fee>,
    }
}
```

```javascript WIP
{
  "set_fee": {
    "flash_loan_fee": {
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
| `flash_loan_fee`\* | Fee | New fee value for fees associated with non-whitelisted flashloans |
| `warchest_fee`\* | Fee | New fee values for fee allocation to the Warchest |

\* = optional

### UpdateState

Update the state of the contract with the provided arguments.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateState {
        anchor_money_market_address: Option<String>,
        aust_address: Option<String>,
        profit_check_address: Option<String>,
        allow_non_whitelisted: Option<bool>,
    }
}
```

```javascript WIP
{
  ...
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `anchor_money_market_address`\* | String | New address for the anchor money market |
| `aust_address`\* | String | New aUST address |
| `profit_check_address`\* | String | New profit check contract address |
| `allow_non_whitelisted`\* | bool | New boolean for allowing non-whitelisted contracts to take out flashloans |

\* = optional

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

```javascript
{
  "withdraw_liquidity": {}
}
```

## Callbacks

Callback Functions are used in conjunction with other ExecuteMsg functions in the Stablecoin Vault.

### `AfterSuccessfulLoanCallback`

After a successful loan happens, check if the contract currently holds more than the STABLE_CAP * 1.5. If it does, Leave STABLE_CAP in UST and deposit all other UST in Anchor.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum CallbackMsg {
    AfterSuccessfulLoanCallback {},
}
```

```javascript
{
  "after_successful_loan_callback": {}
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
    pub flash_loan_fee: Fee,
    pub warchest_fee: Fee,
    pub warchest_addr: CanonicalAddr
}
```

```javascript WIP
{
  
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `community_fund_fee` | CappedFee | Fee information related to Community Fund fee distribution. |
| `warchest_fee` | Fee | Fee information related to Warchest fee distribution. |
| `community_fund_addr` | CanonicalAddr | Address of the Community Fund Contract |
| `warchest_addr` | CanonicalAddr | Address of the Community Fund Contract |

### `EstimateWithdrawFee`

Query to provide an estimate of the Stablecoin Vault withdrawal fee minus tax.

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

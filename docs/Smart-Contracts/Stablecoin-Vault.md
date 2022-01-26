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
    pub treasury_addr: String,
    pub asset_info: AssetInfo,
    pub token_code_id: u64,
    pub treasury_fee: Decimal,
    pub flash_loan_fee: Decimal,
    pub commission_fee: Decimal,
    pub stable_cap: Uint128,
    pub vault_lp_token_name: Option<String>,
    pub vault_lp_token_symbol: Option<String>,
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `anchor_money_market_address` | String | Contract address of Anchor Money Market system. This is used for depositing and withdrawing from Anchor |
| `aust_address` | String | Contract address of aUST token |
| `profit_check_address` | String | Contract address of Profit Check Contract |
| `treasury_address` | String | Contract address of Treasury Contract |
| `asset_info` | AssetInfo | Struct detailing the token to be used for trading (the vault base token) |
| `token_code_id` | u64 | The Stored Code Object ID for the LP token creator. This is used on instantiation to creation an LP token when the Vault is created |
| `treasury_fee` | Decimal | Configurable fee rate for the treasury contract. |
| `commission_fee` | Decimal | Fee on every profitable action on the vault, sent to the treasury. |
| `stable_cap` | Uint128 | Initial UST_CAP value which represents the amount of liquid UST kept outside of Anchor |
| `vault_lp_token_name` | Uint128 | Absolute max rate for community fund |
| `vault_lp_token_symbol` | Uint128 | Absolute max rate for community fund |

## ExecuteMsg

```rust 
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Receive(Cw20ReceiveMsg),
    ProvideLiquidity {
        asset: Asset,
    },
    SetStableCap {
        stable_cap: Uint128,
    },
    SetFee {
        flash_loan_fee: Option<Fee>,
        treasury_fee: Option<Fee>,
        commission_fee: Option<Fee>
    },
    SetAdmin {
        admin: String,
    },
    AddToWhitelist {
        contract_addr: String,
    },
    RemoveFromWhitelist {
        contract_addr: String,
    },
    UpdateState {
        anchor_money_market_address: Option<String>,
        aust_address: Option<String>,
        profit_check_address: Option<String>,
        allow_non_whitelisted: Option<bool>,
    },
    FlashLoan {
        payload: FlashLoanPayload,
    },
    Callback(CallbackMsg),
}
```

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

### FlashLoan

Attempt to get a flashloan from the vault. The requested funds and callback message are encapsuled in the FlashLoanPayload struct. The provided callback is required to be an ExecuteMessage on contract requesting the funds. 

If needed, funds are withdrawn from anchor. All incured fees are paid by the borrower. Two calls to the profit_check contract surround the flashloan callback msg to ensure the trade only finalizes if the contract makes a profit.

| Key | Type | Description |
| :--- | :--- | :--- |
| `requested_asset` | Asset | Requested asset, contains amount an assetinfo |
| `callback` | Binary | The encoded callback msg provided by the calling contract |

```javascript
{
  "requested_asset": {
      "info": {
          "denom": "uusd"
      }
      "amount": "1000000"
  }, 
  "callback": "<binary>", 
}
```


### ProvideLiquidity

Attempt to perform a deposit into the vault by providing UST. White Whale does not charge any additional fees on depositing.
In the event of a successful deposit, the address will receive newly minted LP tokens representing their share of the Vault's liquidity. These LP tokens are then required when an account wishes to withdraw liquidity.

This function should be called alongside a deposit of UST into the contract.


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
        }
}
```

### SetStableCap

Change the UST_CAP parameter for the Stablecoin Vault which represents the cap of liquid UST kept in the vault. Deposits in excess of the cap will be deposited into anchor. Can only be called by the established Admin of the contract.


| Key | Type | Description |
| :--- | :--- | :--- |
| `stable_cap` | Uint128 | New UST_CAP value to be set |

### SetAdmin

Change the established Admin for the Stablecoin Vault. Can only be called by the currently established Admin of the contract.

| Key | Type | Description |
| :--- | :--- | :--- |
| `admin` | Addr | Address of the new Admin. |

### AddToWhitelist

Adds the provided address to the whitelist. Can only be called by Admin.

| Key | Type | Description |
| :--- | :--- | :--- |
| `contract_addr` | Addr | Address of the new whitelisted contract. |

### RemoveFromWhitelist

Removes the provided address from the whitelist. Can only be called by Admin.


| Key | Type | Description |
| :--- | :--- | :--- |
| `contract_addr` | Addr | Address of the whitelisted contract. |

### SetFee

Update the fee information for 1 or more of the associated fee structures. Can only be called by Admin.


| Key | Type | Description |
| :--- | :--- | :--- |
| `flash_loan_fee`\* | Fee | New fee value for fees associated with non-whitelisted flashloans |
| `treasury_fee`\* | Fee | New fee values for fee allocation to the Treasury |

\* = optional

### UpdateState

Update the state of the contract with the provided arguments.

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

Attempt to withdraw deposits. Fees are calculated and deducted. If the Vault has available funds in Anchor it will attempt to payout the withdrawal request by first withdrawing some funds from Anchor to fund the withdrawal request. Luna holdings are not eligible for withdrawal. LP tokens submitted with a withdrawal request will be burned. The Treasury withdraw fee is paid out by transfering ownership of a fraction of the LP tokens to the treasury contract. 

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum Cw20HookMsg {
    WithdrawLiquidity {},
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

## QueryMsg

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> StdResult<Binary> {
    match msg {
        QueryMsg::PoolConfig {} => to_binary(&try_query_config(deps)?),
        QueryMsg::PoolState {} => to_binary(&try_query_pool_state(deps)?),
        QueryMsg::State {} => to_binary(&try_query_state(deps)?),
        QueryMsg::Fees {} => to_binary(&query_fees(deps)?),
        QueryMsg::VaultValue {} => to_binary(&query_total_value(deps)?),
        QueryMsg::EstimateWithdrawFee { amount } => {
            to_binary(&estimate_withdraw_fee(deps, amount)?)
        }
    }
}
```


### `State`

Returns the contract state.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    State {} 
}
```
### `VaultValue`

Returns the total value of UST and aUST held in the contract in UST.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    VaultValue {} 
}
```

### `PoolConfig`

Gets the configuration for the Stablecoin Vault contract pool.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    PoolConfig {} 
}
```
### `PoolInfo`

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct PoolInfo {
    pub asset_infos: [AssetInfo; 2],
    pub contract_addr: Addr,
    pub liquidity_token: Addr,
    pub stable_cap: Uint128
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `asset_infos` | [AssetInfo; 2] | Collection of Assets in the Pool and their information. In our case UST and aUST |
| `contract_addr` | Addr | This contract's address |
| `liquidity_token` | Addr | Address of the LP token |
| `stable_cap` | Uint128 | Stablecoin reserve/cap amount |

### `PoolState`

Gets the pool state, including the balances of the holdings.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    PoolState {} 
}
```

### `PoolResponse`

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct PoolResponse {
    pub asset_infos: [Asset; 2],
    pub contract_addr: Addr,
    pub liquidity_token: Addr,
    pub stable_cap: Uint128
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `asset_infos` | [AssetInfo; 2] | Collection of Assets in the Pool and their information. In our case UST and aUST |
| `contract_addr` | Addr | This contract's address |
| `liquidity_token` | Addr | Address of the LP token |
| `stable_cap` | Uint128 | Stablecoin reserve/cap amount |

### `Fees`

Gets the fees structure set by the Stablecoin Vault contract.
### `FeeResponse`

```rust
pub struct FeeResponse {
    pub fees: VaultFee,
}
```

### `VaultFee`

| Key | Type | Description |
| :--- | :--- | :--- |
| `flash_loan_fee` | Fee | Flashloan fee expected to be paid by non-whitelisted contracts |
| `treasury_fee` | Fee | Fee information related to Treasury fee distribution. |
| `treasury_addr` | CanonicalAddr | Address of the Community Fund Contract |

### `EstimateWithdrawFee`

Query to provide an estimate of the Stablecoin Vault withdrawal fee minus tax.

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Uint128 | Deposit amount to calculate a deposit fee for |

### `EstimateWithdrawFeeResponse`

```rust
pub struct EstimateWithdrawFeeResponse {
    pub fee: Vec<Coin>,
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `fee` | Vec | Vector containing fee info in the form of Coins |

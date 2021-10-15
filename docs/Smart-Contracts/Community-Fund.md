# Community

The Community fund contract holds the funds of the [Community Fund](../../protocol/anchor-governance/), which can be spent through governance polls. It also has control over the protocol owned liquidity.
It is controlled by the governance contract and serves to both grow its holdings and give grants to proposals submitted by community members.

## Config

| Name | Type | Description |
| :--- | :--- | :--- |
| `whale_token_addr` | CanonicalAddr | Contract address of Whale Token |
| `whale_pool_addr` | CanonicalAddr | Contract address of Whale UST Pool |
| `anchor_money_market_addr` | CanonicalAddr | Contract address of Anchor money market module |
| `aust_addr` | CanonicalAddr | Contract address of Anchor UST token |
| `deposits_in_uusd` | Uint128 | Upper cap on community grant size |
| `last_deposit_in_uusd` | Uint128 | Upper cap on community grant size |
| `anchor_deposit_threshold` | Uint128 | The deposit threshold determines the minimum amount of UST the contract has to own before it can deposit those funds into Anchor. |
| `anchor_withdraw_threshold` | Uint128 | The withdraw threshold determines the minimum amount of aUST the contract has to own before it can withdraw those funds from Anchor. |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub whale_token_addr: String,
    pub whale_pair_addr: String,
    pub anchor_money_market_addr: String,
    pub aust_addr: String,
    pub anchor_deposit_threshold: Uint128,
    pub anchor_withdraw_threshold: Uint128,
}
```

```javascript
{
  "whale_token_addr": "terra1...", 
  "whale_pair_addr": "terra1...", 
  "anchor_money_market_addr": "terra1...", 
  "aust_addr": "terra1...", 
  "anchor_deposit_threshold": "100000000000",
  "anchor_withdraw_threshold": "100000000000" 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `whale_token_addr` | CanonicalAddr | Contract address of Whale Token |
| `whale_pool_addr` | CanonicalAddr | Contract address of Whale UST Pool |
| `anchor_money_market_addr` | CanonicalAddr | Contract address of Anchor money market module |
| `aust_addr` | CanonicalAddr | Contract address of Anchor UST token |
| `anchor_deposit_threshold` | Uint128 | The deposit threshold determines the minimum amount of UST the contract has to own before it can deposit those funds into Anchor |
| `anchor_withdraw_threshold` | Uint128 | The withdraw threshold determines the minimum amount of aUST the contract has to own before it can withdraw those funds from Anchor |

## ExecuteMsg

### `UpdateAdmin`

Updates the Collector contract admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateAdmin {
        admin: String, 
    }
}
```

```javascript
{
  "update_admin": {
    "admin": "terra1..." 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `admin` | String | Address of the new contract admin |

### `Spend`

Transfers WHALE tokens to the grant recipient. Can only be issued by the the registered admin which is intended to be the Governance contract.

The Spend function enables the Community Fund to provide grants to community members and on objectives defined and voted on through governance.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Spend {
        recipient: String, 
        amount: Uint128, 
    }
}
```

```javascript
{
  "spend": {
    "recipient": "terra1...", 
    "amount": "100000000" 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `recipient` | String | Recipient of community grant |
| `amount` | Uint128 | Community grant amount |

### `Burn`

Burns a specified amount of WHALE tokens. Can only be issued by the the registered admin which is intended to be the Governance contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Burn {
        amount: Uint128, 
    }
}
```

```javascript
{
  "burn": {
    "amount": "100000000" 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `amount` | Uint128 | Amount of WHALE to burn |

### `Deposit`

Deposit enables the deposit of UST or depending on the amount of interest in the fund may perform a purchase of WHALE tokens. The deposit function first performs two checks, that only 1 type of token is being sent in and that that token is UST.

Every time a deposit is triggered, a check is performed on the amount of aUST in the vault which represents the amount of UST deposited into Anchor. If the `anchor deposit value < total UST deposited + threshold` then the funds will be deposited into Anchor.
Alternatively if the threshold is exceeded, instead, the interest is calculated and is then spent. First a withdrawal is performed for an amount which represents the excess interest in Anchor. After the withdrawal, another message is prepared and executed which buys WHALE tokens with the withdrawn UST.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Deposit {
    }
}
```

```javascript
{
  "deposit": {
  }
}
```

### `UpdateAnchorDepositThreshold`

Updates the Community Fund's deposit threshold value which determines the minimum amount of UST the contract has to own before it can deposit funds into Anchor.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateAnchorDepositThreshold {
        threshold: Uint128, 
    }
}
```

```javascript
{
  "update_anchor_deposit_threshold": {
    "threshold": "100" 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `threshold` | Uint128 | New deposit threshold value |

### `UpdateAnchorWithdrawThreshold`

Updates the Community Fund's withdraw threshold value which determines the minimum amount of aUST the contract has to own before it can withdraw funds from Anchor.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateAnchorWithdrawThreshold {
        threshold: Uint128, 
    }
}
```

```javascript
{
  "update_anchor_withdraw_threshold": {
    "threshold": "100" 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `threshold` | Uint128 | New withdraw threshold value |

## QueryMsg

### `Config`

Gets the Community Fund contract configuration.

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

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `ConfigResponse`

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ConfigResponse {
    pub token_addr: Addr,
    pub pool_addr: Addr,
    pub anchor_money_market_addr: Addr,
    pub aust_addr: Addr,
    pub anchor_deposit_threshold: Uint128,
    pub anchor_withdraw_threshold: Uint128,
}
```

```javascript
{
  "token_addr": "terra1...", 
  "pool_addr": "terra1...", 
  "anchor_money_market_addr": "terra1...", 
  "aust_addr": "terra1...", 
  "anchor_deposit_threshold": "100000000000",
  "anchor_withdraw_threshold": "100000000000" 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `token_addr` | CanonicalAddr | Contract address of Whale Token |
| `pool_addr` | CanonicalAddr | Contract address of Whale UST Pool |
| `anchor_money_market_addr` | CanonicalAddr | Contract address of Anchor money market module |
| `aust_addr` | CanonicalAddr | Contract address of Anchor UST token |
| `anchor_deposit_threshold` | Uint128 | The deposit threshold determines the minimum amount of UST the contract has to own before it can deposit those funds into Anchor |
| `anchor_withdraw_threshold` | Uint128 | The withdraw threshold determines the minimum amount of aUST the contract has to own before it can withdraw those funds from Anchor |

### `Admin`

Gets the Community Fund contract registered Admin. On launch and thereafter this should be the Governance contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Admin {}
}
```

```javascript
{
  "admin": {}
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `AdminResponse`

> Note: The AdminResponse object is imported from the `cw_controllers` package. This definition may change
> as that package is updated

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Debug)]
pub struct AdminResponse {
    pub admin: Option<String>,
}
```

```javascript
{
  "admin": "terra1...", 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `admin` | CanonicalAddr | Contract address of registered Admin of the contract |

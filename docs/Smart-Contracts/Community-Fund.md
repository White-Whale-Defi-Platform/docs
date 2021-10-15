# Community

The Community Contract holds the funds of the [Community Fund](../../protocol/anchor-governance/), which can be spent through a governance poll.

## Config

| Name | Type | Description |
| :--- | :--- | :--- |
| `whale_token_addr` | CanonicalAddr | Contract address of Whale Token |
| `whale_pool_addr` | CanonicalAddr | Contract address of Whale UST Pool |
| `anchor_money_market_addr` | CanonicalAddr | Contract address of Anchor money market module |
| `aust_addr` | CanonicalAddr | Contract address of Anchor UST token |
| `deposits_in_uusd` | Uint128 | Upper cap on community grant size |
| `last_deposit_in_uusd` | Uint128 | Upper cap on community grant size |
| `anchor_deposit_threshold` | Uint128 | Threshold value which dictates whether the funds should be deposited into Anchor or not. Not every deposit to the fund will be deposited into Anchor for example micro-deposits |
| `anchor_withdraw_threshold` | Uint128 | TODO: Finish. |

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
| `anchor_deposit_threshold` | Uint128 | Threshold value which dictates whether the funds should be deposited into Anchor or not. Not every deposit to the fund will be deposited into Anchor for example micro-deposits |
| `anchor_withdraw_threshold` | Uint128 | TODO: Finish. |

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

Deposit enables the deposit of UST and depending on the amount of interest in the fund may perform a purchase of WHALE tokens. The deposit function first performs two checks, that only 1 type of token is being sent in and that that token is UST.

Every time a deposit is triggered, a check is performed on the amount of aUST in the vault which represents the amount of UST deposited into Anchor. If the value of the aUST balance in USD is less than than the amount of deposits in USD + the configurable withdrawal threshold then the funds will be deposited into Anchor.
Alternatively if the threshold is exceeded, instead, the interest is spent. First a withdrawal is performed for an amount which represents the excess interest in Anchor. After the withdrawal, another message is prepared and executed which buys WHALE tokens with the withdrawn UST.

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

pub enum ExecuteMsg {
    Spend{ recipient: String, amount: Uint128 },
    Burn{ amount: Uint128 },
    Deposit {},
    UpdateAdmin{ admin: String },
    UpdateAnchorDepositThreshold{ threshold: Uint128 },
    UpdateAnchorWithdrawThreshold{ threshold: Uint128 },
}
## QueryMsg

### `Config`

Gets the Collector contract configuration.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {}
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "config": {}
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `ConfigResponse`

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ConfigResponse {
    pub gov_contract: String,
    pub anchor_token: String,
    pub spend_limit: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "gov_contract": "terra1...", 
  "anchor_token": "terra1...", 
  "spend_limit": "100000000000" 
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| `gov_contract` | String | Contract address of Gov |
| `anchor_token` | String | Contract address of Anchor Token \(ANC\) |
| `spend_limit` | Uint128 | Upper cap on community grant size |

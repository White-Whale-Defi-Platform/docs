# Community

The Community fund contract holds the funds of the [Community Fund](../../protocol/anchor-governance/), which can be spent through governance polls. It also has control over the protocol owned liquidity.
It is controlled by the governance contract and serves to both grow its holdings and give grants to proposals submitted by community members.

## Config

| Name | Type | Description |
| :--- | :--- | :--- |
| `whale_token_addr` | CanonicalAddr | Contract address of Whale Token |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub whale_token_addr: String,
}
```

```javascript
{
  "whale_token_addr": "terra1...", 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `whale_token_addr` | CanonicalAddr | Contract address of Whale Token |

## ExecuteMsg

### `SetAdmin`

Updates the Community fund contract admin.

> Note: The AdminResponse object is imported from the `cw_controllers` package. This definition may change
> as that package is updated

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SetAdmin {
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
}
```

```javascript
{
  "token_addr": "terra1...", 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `token_addr` | CanonicalAddr | Contract address of Whale Token |

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

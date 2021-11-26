# Treasury

The treasury behaves similarly to a community fund with the proviso that funds in the treasury are used to provide staking rewards to stakers.
It is controlled by the governance contract and serves to grow its holdings and become a safeguard/protective measure in keeping the peg.

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

### `AddTrader`

Registers a contract address as a registered Trader

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AddTrader {
        trader: String, 
    }
}
```

```javascript
{
  "add_trader": {
    "trader": "terra1..." 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `trader` | String | Address of the account to register as a trader |

### `RemoveTrader`

Updates the Community fund contract admin.

> Note: The AdminResponse object is imported from the `cw_controllers` package. This definition may change
> as that package is updated

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    RemoveTrader {
        trader: String, 
    }
}
```

```javascript
{
  "remove_trader": {
    "trader": "terra1..." 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `trader` | String | Address of the registered trader to remove |

### `TraderAction`

Take one or more `msgs` as a vec and then attempt to execute these actions on behalf of a whitelisted addr

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    TraderAction {
        msgs: Vec<CosmosMsh<Empty>>, 
    }
}
```

```javascript
{
  "trader_action": {
    "msgs": []
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `msg` | Vec<CosmosMsh<Empty>> | Vector of msgs to be executed |

### `UpdateAssets`

Update the stored asset information for the treasury

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateAssets {
        to_add: Vec<VaultAsset>,
        to_remove: Vec<AssetInfo>,
    }
}
```

```javascript
{
  "trader_action": {
    "msgs": []
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `to_add` | Vec<VaultAsset> | Vector of assets to be added |
| `to_remove` | Vec<AssetInfo> | Vector of asset infos to be removed |

## QueryMsg

### `Config`

Gets the Treasury contract configuration.

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

### `TotalValue`

Gets the total value of assets in the Treasury.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    TotalValue {}
}
```

```javascript
{
  "total_value": {}
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `ValueResponse`

tbd 

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Debug)]
pub struct ValueResponse {
    
}
```

```javascript
{
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `HoldingValue`

Computes and returns the value of a specified asset that is held by the vault.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    HoldingValue {
        identifier: String
    }
}
```

```javascript
{
  "holding_value": {
      "identifier": ""
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `HoldingValueResponse`

tbd 

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Debug)]
pub struct HoldingValueResponse {
    
}
```

```javascript
{
  "admin": "terra1...", 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `VaultAssetConfig`

Returns the registered Assets of the treasury vault.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    VaultAssetConfig {
    }
}
```

```javascript
{
  "vault_asset_config": {
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `VaultAssetConfigResponse`

tbd 

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Debug)]
pub struct VaultAssetConfigResponse {
    
}
```

```javascript
{
  "admin": "terra1...", 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |
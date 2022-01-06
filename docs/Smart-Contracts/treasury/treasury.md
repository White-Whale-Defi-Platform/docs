# Treasury

The Treasury contract is the holder of the Protocol Owned Liquidity and is able to maintain a series of registered helper smart contracts, which we call 'dapps', to prepare messages for various operations. Only whitelisted addresses are able to forward messages for execution.

The treasury also has internal value calculation logic. This logic is enable by the VaultAsset class in which each VaultAsset, apart from the base reference (UST for example), has a value reference (ValueRef). The value reference provides a way to calculate the value of the asset held in the treasury given a correct configuration.   

## Config

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
}
```


| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

## ExecuteMsg

```rust
pub enum ExecuteMsg {
    /// Sets the admin
    SetAdmin { admin: String },
    /// Executes the provided messages if sender is whitelisted
    DAppAction { msgs: Vec<CosmosMsg<Empty>> },
    /// Adds the provided address to whitelisted dapps
    AddDApp { dapp: String },
    /// Removes the provided address from the whitelisted dapps
    RemoveDApp { dapp: String },
    /// Updates the VAULT_ASSETS map
    UpdateAssets {
        to_add: Vec<VaultAsset>,
        to_remove: Vec<AssetInfo>,
    },
}
```

### `SetAdmin`

Updates the Treasury contract admin.

> Note: The AdminResponse object is imported from the `cw_controllers` package. This definition may change
> as that package is updated


```javascript
{
  "set_admin": {
    "admin": "terra1..." 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `admin` | String | Address of the new contract admin |

### `AddDApp`

Registers a contract address as a registered Dapp

```javascript
{
  "add_d_app": {
    "dapp": "terra1..." 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `dapp` | String | Address of the dapp to register. |

### `RemoveDApp`

Deregistered an already registered Dapp using its contract address.


```javascript
{
  "remove_d_app": {
    "dapp": "terra1..." 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `dapp` | String | Address of the registered dapp to remove |

### `DappAction`

Take one or more `msgs` as a vec and then attempt to execute these actions on behalf of a whitelisted address.

```javascript
{
  "d_app_action": {
    "msgs": []
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `msg` | Vec[CosmosMsg[Empty]] | Vector of msgs to be executed |

### `UpdateAssets`

Update the stored asset information for the treasury. This information is only used to calculate the treasury holding value. 


```json
{
    "update_assets": {
        "to_add": [
          {"asset":{
            "info": {
              "native_token":
              {"denom": "uusd"}
            },
            "amount": "0"
            }, 
            "value_reference": null
          }
        ],
        "to_remove": [{"denom":"uusd"}]
    }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `to_add` | Vec[VaultAsset] | Vector of assets to be added |
| `to_remove` | Vec[AssetInfo] | Vector of asset infos to be removed |

## QueryMsg

```rust
pub enum QueryMsg {
    /// Returns the treasury Config
    Config {},
    /// Returns the total value of all held assets
    TotalValue {},
    /// Returns the value of one specific asset
    HoldingValue { identifier: String },
    /// Returns the amount of specified tokens this contract holds
    HoldingAmount { identifier: String },
    /// Returns the VAULT_ASSETS value for the specified key
    VaultAssetConfig { identifier: String },
}
```

### `Config`

Returns all the permissioned dapps

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
    pub dapps: Vec<String>,
}
```

```javascript
{
  "dapps": [], 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `dapps` | Vec<String> | Vec containing a list of registered dapps |

### `TotalValue`

Gets the total value of assets in the Treasury.

```javascript
{
  "total_value": {}
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `TotalValueResponse`



```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Debug)]
pub struct TotalValueResponse {
    value: Uint128
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

```javascript
{
  "holding_value": {
      "identifier": "terra1dfs..."
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `HoldingValueResponse`


```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Debug)]
pub struct HoldingValueResponse {
    value: Uint128
}
```

```javascript
{
  "value": "...", 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `HoldingAmount`

Returns the amount of a specified asset that is held by the vault.

```javascript
{
  "holding_value": {
      "identifier": "terra1dfs..."
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `VaultAssetConfig`

Returns the registered Assets of the treasury vault.

```javascript
{
  "vault_asset_config": {
    "identifier": "terra1dfs..."
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

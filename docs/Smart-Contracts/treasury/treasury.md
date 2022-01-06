# Treasury

The Treasury contract is the holder of the Protocol Owned Liquidity and is able to maintain a series of registered helper smart contracts, we call 'dapps' to prepare messages for various operations the treasury will execute.

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

### `SetAdmin`

Updates the Community fund contract admin.

> Note: The AdminResponse object is imported from the `cw_controllers` package. This definition may change
> as that package is updated


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

### `AddDapp`

Registers a contract address as a registered Dapp

```javascript
{
  "add_dapp": {
    "dapp": "terra1..." 
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `dapp` | String | Address of the dapp to register. |

### `RemoveDapp`

Deregistered an already registered Dapp using its contract address.


```javascript
{
  "remove_dapp": {
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
  "trader_action": {
    "msgs": []
  }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `msg` | Vec<CosmosMsg<Empty>> | Vector of msgs to be executed |

### `UpdateAssets`

Update the stored asset information for the treasury. Attempt to add and/or remove 1 or more Asset types from the memory contract. This call can be used to store Assets which will be used by the Dapp such as storing a custom CW20 including its address ready for later use.


```json
{
    "update_assets": {
        "to_add": [{"asset":{}, "value_reference": null}],
        "to_remove": [{"denom":"uusd"}]
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
    pub traders: Vec<String>,
}
```

```javascript
{
  "traders": [], 
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `traders` | Vec<String> | Vec containing a list of registered traders |

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

### `ValueResponse`

tbd 

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Debug)]
pub struct ValueResponse {
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

### `VaultAssetConfig`

Returns the registered Assets of the treasury vault.

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
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
|  |  |  |
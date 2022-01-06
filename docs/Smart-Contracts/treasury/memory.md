# Memory 

The memory contract represents an abstraction around of use and storage of contract address and Asset information to be used by a relevant dapp or contract. 
With this request-response model around addresses and assets we gain a small piece of assurance against human error such as mistyped addresses as well as gaining the ability to have many dapps requesting asset info from a common source.

## Config 

### BaseState

See [Dapp BaseState](../Dapp-Base/#basestate)

## InstantiateMsg

No values are needed to instantiate this contract. The sender of the instantiate msg will be marked as the Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub struct InstantiateMsg {}
```

## ExecuteMsg

```rust
pub fn execute(deps: DepsMut, _env: Env, info: MessageInfo, msg: ExecuteMsg) -> MemoryResult {
    match msg {
        ExecuteMsg::SetAdmin { admin } => set_admin(deps, info, admin),
        ExecuteMsg::UpdateContractAddresses { to_add, to_remove } => {
            update_contract_addresses(deps, info, to_add, to_remove)
        }
        ExecuteMsg::UpdateAssetAddresses { to_add, to_remove } => {
            update_asset_addresses(deps, info, to_add, to_remove)
        }
    }
}
```

### `SetAdmin`

Update the configured admin for the memory contract. 

| Key | Type | Description |
| :--- | :--- | :--- |
| `admin` | String | Address of the new Admin. |

```json 
{
    "set_admin": {
        "admin": "terra1..."
    }
}
```

### `UpdateContractAddresses`

Attempt to add and/or remove 1 or more contract address from the memory contract. This call can be used to store contracts one by one or in one go to store many contracts on instantiation of a Dapp.


| Name | Type | Description |
| :--- | :--- | :--- |
| `to_add` | Vec<(String, String)> | Vector containing a pair mapping for each contract address to be added. |
| `to_remove` | Vec<(String)> | Vector containing memory keys/ids for each contract address to be removed from Memory. |

```json
{
    "update_contract_addresses": {
        "to_add": [("mykey", "terra1...")],
        "to_remove": ["key_to_remove"]
    }
}
```

### `UpdateContractAssets`

Attempt to add and/or remove 1 or more Asset types from the memory contract. This call can be used to store Assets which will be used by the Dapp such as storing a custom CW20 including its address ready for later use.


| Name | Type | Description |
| :--- | :--- | :--- |
| `to_add` | Vec<(String, String)> | Vector containing a pair mapping for each Asset to be added. |
| `to_remove` | Vec<(String)> | Vector containing memory keys/ids for each Asset to be removed from Memory. |

```json
{
    "update_contract_assets": {
        "to_add": [("asset_key", "asset_1")],
        "to_remove": ["key_to_remove"]
    }
}
```

## QueryMsg

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, env: Env, msg: QueryMsg) -> StdResult<Binary> {
    match msg {
        QueryMsg::QueryAssets { names } => queries::query_assets(deps, env, names),
        QueryMsg::QueryContracts { names } => queries::query_contract(deps, env, names),
    }
}
```
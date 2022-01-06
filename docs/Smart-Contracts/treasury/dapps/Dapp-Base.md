
# Dapp Base 

## Config 

### `BaseState`

The BaseState contains the main configurations such as addresses needed for sending messages and querying addresses

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct BaseState {
    pub treasury_address: Addr,
    pub trader: Addr,
    pub memory: Memory,
}
```

## InstantiateMsg

Dapps which are built out from the dapp_base or dapp_template should make use of the provided
`handle_base_init` helper to instantiate the `BaseState`. This ensures a common structure for the configuration addresses. You may also choose to save your own state or config values as needed by defining a second but dapp-specific `State`.

```rust
/// Handles creates the State and Memory object and returns them.
pub fn handle_base_init(deps: Deps, msg: BaseInstantiateMsg) -> StdResult<BaseState> {
    // Memory
    let memory = Memory {
        address: deps.api.addr_validate(&msg.memory_addr)?,
    };
    // Base state
    let state = BaseState {
        treasury_address: deps.api.addr_validate(&msg.treasury_address)?,
        trader: deps.api.addr_validate(&msg.trader)?,
        memory,
    };

    Ok(state)
}
```

## ExecuteMsg 

Dapps which are built out from the dapp_base or dapp_template should make use of the provided
`handle_base_message` helper to handle some common config. This allows each implementation of a dapp to only have to focus on the unique use-cases that particular dapp needs to implement.

Example of 'base' messages handled are Admin changes and changes to the BaseState object.

### `handle_base_message`


```rust
pub fn handle_base_message(
    deps: DepsMut,
    info: MessageInfo,
    message: BaseExecuteMsg,
) -> BaseDAppResult {
    match message {
        BaseExecuteMsg::UpdateConfig {
            treasury_address,
            trader,
            memory,
        } => update_config(deps, info, treasury_address, trader, memory),
        BaseExecuteMsg::SetAdmin { admin } => set_admin(deps, info, admin),
    }
}
```

### `UpdateConfig`

Updates the configuration addresses used by the dapp.

| Key | Type | Description |
| :--- | :--- | :--- |
| `treasury_address`\* | HumanAddr | New address of the associated treasury contract |
| `trader`\* | HumanAddr | New address of trader |
| `memory`\* | HumanAddr | New address of the associated memory contract |

```json
{
    "update_config": {
        "treasury_address": "terra1...",
        "trader": "terra1...", 
        "memory": "terra1..."
    }
}
```

### `SetAdmin`

Change the established Admin for the Dapp. Can only be called by the currently established Admin of the contract.

| Key | Type | Description |
| :--- | :--- | :--- |
| `admin`\* | HumanAddr | New address of the admin |

```json
{
    "set_admin": {
        "admin": "terra1..."
    }
}
```

## QueryMsg

Dapps which are built out from the dapp_base or dapp_template should make use of the provided
`handle_base_query` helper to handle some common config. This means each dapp only needs to implement dapp-specific queries relevant to the use case for a given dapp.

Example of 'base' messages handled are Admin changes and changes to the BaseState object.

### `handle_base_query`


```rust
pub fn handle_base_query(deps: Deps, query: BaseQueryMsg) -> StdResult<Binary> {
    match query {
        BaseQueryMsg::Config {} => to_binary(&try_query_config(deps)?),
    }
}
```

### `Config`

Returns the BaseState which contains the main configuration addresses used by the dapp.

```json
{
    "config": {

    }
}
```

### `BaseStateresponse`

The main configuration addresses used by the dapp.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub struct BaseStateResponse {
    pub treasury_address: String,
    pub trader: String,
    pub memory_address: String,
}
```

```json
{
    "treasury_address": "terra1...",
    "trader": "terra1...",
    "memory_address": "terra1..."
}
```
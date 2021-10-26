# Vesting

The vesting contract handles the WHALE lockup and vesting for the Investor and Team WHALE allocations.

## Config

| Key | Type | Description |
| :--- | :--- | :--- |
| `owner` | CanonicalAddr | Address of contract owner |
| `whale_token` | CanonicalAddr | Contract address of Whale Token \(WHALE\) |
| `genesis_time` | u64 | Block timestamp when Whale Protocol launched |

## InitMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub owner: HumanAddr,
    pub whale_token: HumanAddr,
    pub genesis_time: u64,
}
```

```javascript
{
  "owner": "terra1...", 
  "whale_token": "terra1...", 
  "genesis_time": 123456, 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `owner` | HumanAddr | Address of contract owner |
| `whale_token` | HumanAddr | Contract address of Whale Token \(WHALE\) |
| `genesis_time` | u64 | Block timestamp when Whale Protocol launched |

## ExecuteMsg

### `UpdateConfig`

Updates the Vesting contract configuration. Can only be issued by contract owner.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig {
        owner: Option<HumanAddr>, 
        whale_token: Option<HumanAddr>, 
        genesis_time: Option<u64> 
    }
}
```

```javascript
{
  "update_config": {
    "owner": "terra1...", 
    "whale_token": "terra1...", 
    "genesis_time": 123456 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `owner`\* | HumanAddr | Address of new contract owner |
| `whale_token`\* | HumanAddr | New contract address of Whale Token \(WHALE\) |
| `genesis_time`\* | u64 | New block timestamp when Whale Protocol launched |

\* = optional

### `RegisterVestingAccounts`

Registers a new vesting account to the Vesting contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    RegisterVestingAccounts {
        vesting_accounts: Vec<VestingAccount>, 
    }
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct VestingAccount {
    pub address: HumanAddr,
    pub schedules: Vec<(u64, u64, Uint128)>,
}
```

```javascript
{
  "register_vesting_accounts": {
    "vesting_accounts": [
      {
        "address": "terra1...", 
        "schedules": [123456, 234567, "10000000"]
      }, 
      {
        "address": "terra1...", 
        "schedules": [123456, 234567, "10000000"]
      }
    ]
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `vesting_accounts` | Vec&lt;VestingAccount&gt; | List of accounts with WHALE vesting and their vesting schedules |

| Key | Type | Description |
| :--- | :--- | :--- |
| `address` | HumanAddr | Address with vested WHALE |
| `schedules` | Vec&lt;\(u64, u64, Uint128\)&gt; | Vesting schedule of \(WHALE claimable start time, end time, total vested amount\) |

### `Claim`

Claims vested WHALE tokens. Can only be issued by an account with vested WHALE.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Claim {}
}
```

```javascript
{
  "claim": {} 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

## QueryMsg

### `Config`

Gets the Vesting contract configuration.

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

### `ConfigResponse`

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ConfigResponse {
    pub owner: HumanAddr,
    pub whale_token: HumanAddr,
    pub genesis_time: u64,
}
```

```javascript
{
  "owner": "terra1...", 
  "whale_token": "terra1...", 
  "genesis_time": 123456 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `owner` | HumanAddr | Address of contract owner |
| `whale_token` | HumanAddr | Contract address of Whale Token \(WHALE\) |
| `genesis_time` | u64 | Block timestamp when Whale Protocol launched |

### `VestingAccount`

Gets vesting information for an account with vested WHALE. Response includes the claim address and related vesting information such as schedules for claiming.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    VestingAccount {
        address: HumanAddr, 
    }
}
```

```javascript
{
  "vesting_account": {
    "address": "terra1..." 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `address` | HumanAddr | Address with WHALE vested |

### `VestingAccountResponse`

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct VestingAccountResponse {
    pub address: HumanAddr,
    pub info: VestingInfo,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct VestingInfo {
    pub schedules: Vec<(u64, u64, Uint128)>,
    pub last_claim_time: u64,
}
```

```javascript
{
  "address": "terra1...", 
  "info": {
    "schedules": [123456, 234567, "10000000"], 
    "last_claim_time": 123456, 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `address` | HumanAddr | Address with WHALE vested |
| `info` | VestingInfo | Vesting information |

| Key | Type | Description |
| :--- | :--- | :--- |
| `schedules` | Vec&lt;\(u64, u64, Uint128\)&gt; | Vesting schedule of \(WHALE claimable start time, end time, total vested amount\) |
| `last_claim_time` | u64 | Block timestamp when this account last claimed WHALE |

### `VestingAccounts`

Gets vesting information for accounts with vested WHALE. The response includes all accounts registered in the vesting contract which meet the query conditions. Each account returned in the response includes the claim address and related vesting information such as schedules for claiming.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    VestingAccounts {
        start_after: Option<HumanAddr>, 
        limit: Option<u32>, 
        order_by: Option<OrderBy>, 
    }
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum OrderBy {
    Asc,
    Desc,
}
```

```javascript
{
  "vesting_accounts": {
    "start_after": "terra1...", 
    "limit": 8, 
    "order_by": "asc"
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `start_after`\* | HumanAddr | Address with WHALE vested to start query |
| `limit`\* | u32 | Maximum number of query entries |
| `order_by`\* | OrderBy | Order to make query |

| Key | Description |
| :--- | :--- |
| `Asc` | Make query in ascending order |
| `Desc` | Make query in descending order |

\* = optional

### `VestingAccountsResponse`

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct VestingAccountsResponse {
    pub vesting_accounts: Vec<VestingAccountResponse>,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct VestingAccountResponse {
    pub address: HumanAddr,
    pub info: VestingInfo,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct VestingInfo {
    pub schedules: Vec<(u64, u64, Uint128)>,
    pub last_claim_time: u64,
}
```

```javascript
{
  "vesting_accounts": [
    {
      "address": "terra1...", 
      "info": {
        "schedules": [123456, 234567, "10000000"], 
        "last_claim_time": 123456 
      }
    }, 
    {
      "address": "terra1...", 
      "info": {
        "schedules": [123456, 234567, "10000000"], 
        "last_claim_time": 123456 
      }
    }
  ]
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `vesting_accounts` | Vec&lt;VestingAccountResponse&gt; | List of addresses with WHALE vested |

| Key | Type | Description |
| :--- | :--- | :--- |
| `address` | HumanAddr | Address with WHALE vested |
| `info` | VestingInfo | Vesting information |

| Key | Type | Description |
| :--- | :--- | :--- |
| `schedules` | Vec&lt;\(u64, u64, Uint128\)&gt; | Vesting schedule of \(WHALE claimable start time, end time, total vested amount\) |
| `last_claim_time` | u64 | Block timestamp when this account last claimed WHALE |

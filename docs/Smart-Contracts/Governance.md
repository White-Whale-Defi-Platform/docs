# Gov

The Gov Contract contains logic for holding governance polls and handling Whale Token \(WHALE\) staking, and allows White Whale Protocol to be governed by its users in a decentralized manner. After the initial bootstrapping of White Whale Protocol's contracts, the Gov Contract is assigned to be the owner of all contracts in White Whale Protocol.

New proposals for change are submitted as polls, and are voted on by WHALE stakers through the [voting procedure](../protocol/governance/Governance-Overview.md). Polls can contain messages that can be executed directly without changing the White Whale Protocol code.

## References

The Governance contract is based both on the examples set out by the [Mirror Protocol](https://github.com/Mirror-Protocol/mirror-contracts/tree/main/contracts/mirror_gov), [Anchor Protocol](https://github.com/Anchor-Protocol/anchor-token-contracts/tree/main/contracts/gov) and their respective community contracts.

## Config

| Key | Type | Description |
| :--- | :--- | :--- |
| `owner` | CanonicalAddr | Address of contract owner |
| `whale_token` | CanonicalAddr | Contract address of Whale Token \(WHALE\) |
| `quorum` | Decimal | Minimum percentage of participation required for a poll to pass |
| `threshold` | Decimal | Minimum percentage of `yes` votes required for a poll to pass |
| `voting_period` | u64 | Number of blocks during which votes can be cast **\[blocks\]** |
| `timelock_period` | u64 | Number of blocks required after a poll pass before executing changes **\[blocks\]** |
| `expiration_period` | u64 | Number of blocks after a poll's voting period during which the poll can be executed **\[blocks\]** |
| `proposal_deposit` | Uint128 | Minimum WHALE deposit required for submitting a new poll |
| `snapshot_period` | u64 | Window of time \(number of blocks\) allowed for poll snapshot before a poll's end **\[blocks\]** |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub quorum: Decimal,
    pub threshold: Decimal,
    pub voting_period: u64,
    pub timelock_period: u64,
    pub expiration_period: u64,
    pub proposal_deposit: Uint128,
    pub snapshot_period: u64,
}
```

```javascript
{
  "quorum": "0.1", 
  "threshold": "0.5", 
  "voting_period": 123456, 
  "timelock_period": 123456, 
  "expiration_period": 123456, 
  "proposal_deposit": "100000000", 
  "snapshot_period": 123456 
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
| `quorum` | Decimal | Minimum percentage of participation required for a poll to pass |
| `threshold` | Decimal | Minimum percentage of `yes` votes required for a poll to pass |
| `voting_period` | u64 | Number of blocks during which votes can be cast **\[blocks\]** |
| `timelock_period` | u64 | Number of blocks required after a poll pass before executing changes **\[blocks\]** |
| `expiration_period` | u64 | Number of blocks after a poll's voting period during which the poll can be executed **\[blocks\]** |
| `proposal_deposit` | Uint128 | Minimum WHALE deposit required for submitting a new poll |
| `snapshot_period` | u64 | Window of time \(number of blocks\) allowed for poll snapshot before a poll's end **\[blocks\]** |

## ExecuteMsg

### `Receive`

Can be called during a CW20 token transfer when the Gov contract is the recipient. Allows the token transfer to execute a [Receive Hook](Governance.md#receive-hooks) as a subsequent action within the same transaction.

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

```javascript
{
  "receive": {
    "amount": "10000000",
    "sender": "terra1...",
    "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9"
  }
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
| `amount` | Uint128 | Amount of tokens received |
| `sender` | HumanAddr | Sender of token transfer |
| `msg`\* | Binary | Base64-encoded JSON of [Receive Hook](Governance.md#receive-hooks) |

\* = optional

### `RegisterContracts`

Registers the contract addresses \(i.e. Whale Token, WHALE\) to Gov.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    RegisterContracts {
        whale_token: HumanAddr, 
    }
}
```

```javascript
{
  "register_contracts": {
    "whale_token": "terra1...", 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `whale_token` | HumanAddr | Contract address of Whale Token \(WHALE\) |

### `UpdateConfig`

Updates the configuration of the Gov contract.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig {
        owner: Option<HumanAddr>, 
        quorum: Option<Decimal>, 
        threshold: Option<Decimal>, 
        voting_period: Option<u64>, 
        timelock_period: Option<u64>, 
        expiration_period: Option<u64>, 
        proposal_deposit: Option<Uint128>, 
        snapshot_period: Option<u64>,
    }
}
```

```javascript
{
  "update_config": {
    "owner": "terra1...", 
    "quorum": "0.1", 
    "threshold": "0.1", 
    "voting_period": 123456, 
    "timelock_period": 123456, 
    "expiration_period": 123456, 
    "proposal_deposit": "100000000", 
    "snapshot_period": 123456 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `owner`\* | HumanAddr | New address of contract owner |
| `quorum`\* | Decimal | New percentage of participation \(of total staked WHALE\) required for a poll to pass |
| `threshold`\* | Decimal | New percentage of `yes` votes required for a poll to pass |
| `voting_period`\* | u64 | New number of blocks during which votes for a poll can be cast after it has finished its deposit **\[blocks\]** |
| `timelock_period`\* | u64 | New number of blocks required after a poll pass before executing changes **\[blocks\]** |
| `expiration_period`\* | u64 | New number of blocks after a poll's voting period during which the poll can be executed **\[blocks\]** |
| `proposal_deposit`\* | Uint128 | New minimum WHALE deposit required for a poll to enter voting |
| `snapshot_period`\* | u64 | New window of time \(number of blocks\) allowed for poll snapshot before a poll's end **\[blocks\]** |

\* = optional

### `CastVote`

Submits a user's vote for an active poll. Once a user has voted, they cannot change their vote with subsequent messages \(increasing voting power, changing vote option, cancelling vote, etc.\)

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    CastVote {
        poll_id: u64, 
        vote: VoteOption, 
        amount: Uint128, 
    }
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum VoteOption {
    Yes,
    No,
}
```

```javascript
{
  "cast_vote": {
    "poll_id": 8,
    "vote": "yes", 
    "amount": "10000000" 
  }
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
| `poll_id` | u64 | Poll ID |
| `vote` | VoteOption | Can be `yes` or `no` |
| `amount` | Uint128 | Amount of voting power \(staked WHALE\) to allocate |

### `WithdrawVotingTokens`

Removes specified amount of staked WHALE tokens from a staking position and returns them to a user's balance. Withdraws all staked WHALE tokens if `amount` is not specified.


```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    WithdrawVotingTokens {
        amount: Option<Uint128>, 
    }
}
```

```javascript
{
  "withdraw_voting_tokens": {
    "amount": "100000000" 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `amount`\* | Uint128 | Amount of WHALE tokens to withdraw |

\* = optional

### `EndPoll`

Can be issued by anyone to end the voting for an active poll. Triggers tally the results to determine whether the poll has passed. The current block height must exceed the end height of voting phase.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    EndPoll {
        poll_id: u64, 
    }
}
```

```javascript
{
  "end_poll": {
    "poll_id": 8 
  }
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
| `poll_id` | u64 | Poll ID |

### `ExecutePoll`

Can be issued by anyone to implement into action the contents of a passed poll. The current block height must exceed the end height of the poll's effective delay.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ExecutePoll {
        poll_id: u64, 
    }
}
```

```javascript
{
  "execute_poll": {
    "poll_id": 8 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `poll_id` | u64 | Poll ID |

### `ExpirePoll`

Can be issued by anyone to expire a poll. Requires the poll to be neither be a text proposal nor passed. The current block height must be more than the expiration period from the poll's end height.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ExpirePoll {
        poll_id: u64, 
    }
}
```

```javascript
{
  "expire_poll": {
    "poll_id": 8 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `poll_id` | u64 | Poll ID |

### `SnapshotPoll`

Snapshots the total amount of staked WHALE and stores the number to the specified poll. This staked WHALE amount is used to determine the degree of participation for this poll, calculated by dividing the total amount of WHALE voted to the poll with the total staked WHALE supply at the time of [EndPoll](Governance.md#endpoll). Can only be issued within a window of `snapshot_period` blocks before the poll's `end_height`.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SnapshotPoll {
        poll_id: u64, 
    }
}
```

```javascript
{
  "snapshot_poll": {
    "poll_id": 8 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `poll_id` | u64 | Poll ID |

## Receive Hooks

### `StakeVotingTokens`

{% hint style="danger" %}
**WARNING**  
  
Sending WHALE tokens to the Gov contract without issuing this hook will lead to **PERMANENT LOSS OF FUNDS** and will be irrevocably donated to the reward pool for stakers.
{% endhint %}

Issued when sending WHALE tokens to the Gov contract to add them to their WHALE staking position.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum Cw20HookMsg {
    StakeVotingTokens {}
}
```

```javascript
{
  "stake_voting_tokens": {}
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `CreatePoll`

Issued when sending WHALE tokens to the Gov contract to create a new poll. Will only succeed if the amount of tokens sent meets the configured `proposal_deposit` amount. Can contain a list of generic messages to be issued by the Gov contract if it passes \(can invoke messages in other contracts it owns\).

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum Cw20HookMsg {
    CreatePoll {
        title: String, 
        description: String, 
        link: Option<String>, 
        execute_msgs: Option<Vec<ExecuteMsg>>, 
    }
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub struct ExecuteMsg {
    pub order: u64, 
    pub contract: HumanAddr,
    pub msg: Binary,
}
```

```javascript
{
  "create_poll": {
    "title": "...", 
    "description": "...", 
    "link": "https://...", 
    "execute_msgs": [
      {
        "order": 1, 
        "contract": "terra1...", 
        "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9" 
      }, 
      {
        "order": 2, 
        "contract": "terra1...", 
        "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9" 
      } 
    ]
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `title` | String | Poll title |
| `description` | String | Poll description |
| `link`\* | String | URL to external post about poll \(forum, PDF, etc.\) |
| `execute_msgs`\* | Vec&lt;ExecuteMsg&gt; | List of governance messages to be issued by Gov contract upon poll execution |

| Key | Type | Description |
| :--- | :--- | :--- |
| `order` | u64 | Order sequence of message |
| `contract` | HumanAddr | Contract address of governance message recipient |
| `msg` | Binary | Base64-encoded JSON of governance message |

\* = optional

## QueryMsg

### `Config`

Gets the configuration for the Gov contract.


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
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct ConfigResponse {
    pub owner: HumanAddr,
    pub whale_token: HumanAddr,
    pub quorum: Decimal,
    pub threshold: Decimal,
    pub voting_period: u64,
    pub timelock_period: u64,
    pub expiration_period: u64,
    pub proposal_deposit: Uint128, 
    pub snapshot_period: u64, 
}
```

```javascript
{
  "owner": "terra1...", 
  "whale_token": "terra1...", 
  "quorum": "0.1", 
  "threshold": "0.5", 
  "voting_period": 123456, 
  "timelock_period": 123456, 
  "expiration_period": 123456, 
  "proposal_deposit": "100000000", 
  "snapshot_period": 123456 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `owner` | CanonicalAddr | Address of contract owner |
| `whale_token` | CanonicalAddr | Contract address of Whale Token \(WHALE\) |
| `quorum` | Decimal | Minimum percentage of participation required for a poll to pass |
| `threshold` | Decimal | Minimum percentage of `yes` votes required for a poll to pass |
| `voting_period` | u64 | Number of blocks during which votes can be cast **\[blocks\]** |
| `timelock_period` | u64 | Number of blocks required after a poll pass before executing changes **\[blocks\]** |
| `expiration_period` | u64 | Number of blocks after a poll's voting period during which the poll can be executed **\[blocks\]** |
| `proposal_deposit` | Uint128 | Minimum WHALE deposit required for submitting a new poll |
| `snapshot_period` | u64 | Window of time \(number of blocks\) allowed for poll snapshot before a poll's end **\[blocks\]** |

### `State`

Gets state information for the Gov contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    State {}
}
```

```javascript
{
  "state": {}
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
|  |  |  |

### `StateResponse`

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct StateResponse {
    pub poll_count: u64,
    pub total_share: Uint128,
    pub total_deposit: Uint128,
}
```

```javascript
{
  "poll_count": 8, 
  "total_share": "100000000", 
  "total_deposit": "100000000" 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `poll_count` | u64 | Total number of created polls |
| `total_share` | Uint128 | Current total number of voting shares |
| `total_deposit` | Uint128 | Total amount of WHALE currently deposited for poll creation |

### `Staker`

Gets information for the specified WHALE staker.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Staker {
        address: HumanAddr, 
    }
}
```

```javascript
{
  "staker": {
    "address": "terra1..." 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `address` | HumanAddr | Address of staker |

### `StakerResponse`

```rust
#[derive(Serialize, Deserialize, Debug, Clone, PartialEq, JsonSchema)]
pub struct StakerResponse {
    pub balance: Uint128,
    pub share: Uint128,
    pub locked_balance: Vec<(u64, VoterInfo)>, // (Voted Poll's ID, VoterInfo)
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct VoterInfo {
    pub vote: VoteOption,
    pub balance: Uint128,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum VoteOption {
    Yes,
    No,
}
```

```javascript
{
  "balance": "100000000", 
  "share": "100000000", 
  "locked_balance": [
    [
      7, 
      {
        "vote": "yes", 
        "balance": "100000000" 
      }
    ], 
    [
      8, 
      {
        "vote": "no", 
        "balance": "100000000" 
      }
    ] 
  ]
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `balance` | Uint128 | Amount of WHALE staked by staker |
| `share` | Uint128 | Total voting shares owned by staker |
| `locked_balance` | Vec&lt;\(u64, VoterInfo\)&gt; | List of \(voted poll's ID, voter's vote information\) |

| Name | Type | Description |
| :--- | :--- | :--- |
| `vote` | VoteOption | Vote type made by staker |
| `balance` | Uint128 | Amount of staked WHALE locked to vote this poll |

| Name | Description |
| :--- | :--- |
| `yes` | Staker has voted for the proposal |
| `no` | Staker has voted against the proposal |

### `Poll`

Gets information for the specified poll.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Poll {
        poll_id: u64, 
    }
}
```

```javascript
{
  "poll": {
    "poll_id": 8 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `poll_id` | u64 | Poll ID |

### `PollResponse`

```rust
#[derive(Serialize, Deserialize, Debug, Clone, PartialEq, JsonSchema)]
pub struct PollResponse {
    pub id: u64,
    pub creator: HumanAddr,
    pub status: PollStatus,
    pub end_height: u64,
    pub title: String,
    pub description: String,
    pub link: Option<String>,
    pub deposit_amount: Uint128,
    pub execute_data: Option<ExecuteMsg>,
    pub yes_votes: Uint128, // balance
    pub no_votes: Uint128,  // balance
    pub staked_amount: Option<Uint128>, 
    pub total_balance_at_end_poll: Option<Uint128>,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum PollStatus {
    InProgress,
    Passed,
    Rejected,
    Executed,
    Expired,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub struct ExecuteMsg {
    pub order: u64, 
    pub contract: HumanAddr,
    pub msg: Binary,
}
```

```javascript
{
  "id": 8, 
  "creator": "terra1...", 
  "status": "executed", 
  "end_height": 123456, 
  "title": "...", 
  "description": "...", 
  "link": "https://...", 
  "deposit_amount": "100000000", 
  "execute_data": [
    {
      "order": 1, 
      "contract": "terra1...", 
      "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9" 
    }, 
    {
      "order": 2, 
      "contract": "terra1...", 
      "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9" 
    }
  ], 
  "yes_votes": "100000000", 
  "no_votes": "100000000", 
  "staked_amount": "100000000", 
  "total_balance_at_end_poll": "100000000" 
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `id` | u64 | Poll ID |
| `creator` | HumanAddr | Poll creator |
| `status` | PollStatus | Current poll status |
| `end_height` | u64 | Block number when voting for this poll closes **\[block\]** |
| `title` | String | Poll title |
| `description` | String | Poll description |
| `link`\* | String | URL to external post about poll \(forum, PDF, etc.\) |
| `deposit_amount` | Uint128 | WHALE deposit used to submit poll |
| `execute_data`\* | `Vec<ExecuteMsg>` | List of governance messages to be issued upon poll execution |
| `yes_votes` | Uint128 | Total yes votes \(staked WHALE amount\) for this poll |
| `no_votes` | Uint128 | Total no votes \(staked WHALE amount\) for this poll |
| `staked_amount`\* | Uint128 | Total staked WHALE amount at time of poll snapshot |
| `total_balance_at_end_poll`\* | Uint128 | Total staked WHALE amount at the end of this poll |

| Key | Description |
| :--- | :--- |
| `InProgress` | Voting for this poll is currently in progress |
| `Passed` | This poll has been passed by governance |
| `Rejected` | This poll has been rejected by governance |
| `Executed` | This poll has been passed by governance and executed |
| `Expired` | This poll has been expired after rejection / execution |

| Key | Type | Description |
| :--- | :--- | :--- |
| `order` | u64 | Order sequence of message |
| `contract` | HumanAddr | Contract address of governance message recipient |
| `msg` | Binary | Base64-encoded JSON governance message |

\* = optional

### `Polls`

Gets information for all polls.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Polls {
        filter: Option<PollStatus>, 
        start_after: Option<u64>, 
        limit: Option<u32>, 
        order_by: Option<OrderBy>, 
    }
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum PollStatus {
    InProgress,
    Passed,
    Rejected,
    Executed,
    Expired,
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
  "polls": {
    "filter": "passed", 
    "start_after": 8, 
    "limit": 8, 
    "order_by": "asc" 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `filter`\* | PollStatus | Poll statuses to search for |
| `start_after`\* | u64 | Poll ID to start query at |
| `limit`\* | u32 | Maximum number of query entries |
| `order_by`\* | OrderBy | Order to make query |

| Key | Description |
| :--- | :--- |
| `InProgress` | Poll is currently in voting period |
| `Passed` | Poll has been passed by governance |
| `Rejected` | Poll has been rejected by governance |
| `Executed` | Poll has been passed and executed by governance |
| `Expired` | Poll has expired |

| Key | Description |
| :--- | :--- |
| `Asc` | Make query in ascending order |
| `Desc` | Make query in descending order |

\* = optional

### `PollsResponse`

```rust
#[derive(Serialize, Deserialize, Debug, Clone, PartialEq, JsonSchema)]
pub struct PollsResponse {
    pub polls: Vec<PollResponse>,
}

#[derive(Serialize, Deserialize, Debug, Clone, PartialEq, JsonSchema)]
pub struct PollResponse {
    pub id: u64,
    pub creator: HumanAddr,
    pub status: PollStatus,
    pub end_height: u64,
    pub title: String,
    pub description: String,
    pub link: Option<String>,
    pub deposit_amount: Uint128,
    pub execute_data: Option<ExecuteMsg>,
    pub yes_votes: Uint128, // balance
    pub no_votes: Uint128,  // balance
    pub staked_amount: Option<Uint128>, 
    pub total_balance_at_end_poll: Option<Uint128>,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum PollStatus {
    InProgress,
    Passed,
    Rejected,
    Executed,
    Expired,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub struct ExecuteMsg {
    pub order: u32, 
    pub contract: HumanAddr,
    pub msg: Binary,
}
```

```javascript
{
  "polls": [
    {
      "id": 7, 
      "creator": "terra1...", 
      "status": "passed", 
      "end_height": 123456, 
      "title": "...", 
      "description": "...", 
      "link": "https://...", 
      "deposit_amount": "100000000", 
      "execute_data": [
        {
          "contract": "terra1...", 
          "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9" 
        }, 
        {
          "contract": "terra1...", 
          "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9" 
        }
      ], 
      "yes_votes": "100000000", 
      "no_votes": "100000000", 
      "staked_amount": "100000000", 
      "total_balance_at_end_poll": "100000000" 
    }, 
    {
      "id": 8, 
      "creator": "terra1...", 
      "status": "executed", 
      "end_height": 123456, 
      "title": "...", 
      "description": "...", 
      "link": "https://...", 
      "deposit_amount": "100000000", 
      "execute_data": [
        {
          "order": 1, 
          "contract": "terra1...", 
          "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9" 
        }, 
        {
          "order": 2, 
          "contract": "terra1...", 
          "msg": "eyAiZXhlY3V0ZV9tc2ciOiAiYmluYXJ5IiB9" 
        }
      ], 
      "yes_votes": "100000000", 
      "no_votes": "100000000", 
      "staked_amount": "100000000", 
      "total_balance_at_end_poll": "100000000" 
    }
  ]
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `polls` | Vec&lt;PollResponse&gt; | List of poll information |

| Key | Type | Description |
| :--- | :--- | :--- |
| `id` | u64 | Poll ID |
| `creator` | HumanAddr | Poll creator |
| `status` | PollStatus | Current poll status |
| `end_height` | u64 | Block number when voting for this poll closes **\[block\]** |
| `title` | String | Poll title |
| `description` | String | Poll description |
| `link`\* | String | URL to external post about poll \(forum, PDF, etc.\) |
| `deposit_amount` | Uint128 | WHALE deposit used to submit poll |
| `execute_data`\* | Vec&lt;ExecuteMsg&gt; | List of governance messages to be issued upon poll execution |
| `yes_votes` | Uint128 | Total yes votes \(staked WHALE amount\) for this poll |
| `no_votes` | Uint128 | Total no votes \(staked WHALE amount\) for this poll |
| `staked_amount`\* | Uint128 | Total staked WHALE amount at time of poll snapshot |
| `total_balance_at_end_poll`\* | Uint128 | Total staked WHALE amount at the end of this poll |

| Key | Description |
| :--- | :--- |
| `InProgress` | Voting for this poll is currently in progress |
| `Passed` | This poll has been passed by governance |
| `Rejected` | This poll has been rejected by governance |
| `Executed` | This poll has been passed by governance and executed |
| `Expired` | This poll has been expired after rejection / execution |

| Key | Type | Description |
| :--- | :--- | :--- |
| `order` | u64 | Order sequence of message |
| `contract` | HumanAddr | Contract address of governance message recipient |
| `msg` | Binary | Base64-encoded JSON governance message |

\* = optional

### `Voters`

Gets voter information of the poll with the specified ID.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Voters {
        poll_id: u64, 
        start_after: Option<HumanAddr>, 
        limit: Option<u32>, 
        order_by: Option<OrderBy>, 
    }
}
```

```javascript
{
  "voters": {
    "poll_id": 8, 
    "start_after": "terra1..", 
    "limit": 8, 
    "order_by": "asc" 
  }
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `poll_id` | u64 | ID of poll to query voters |
| `start_after`\* | HumanAddr | Address of voter to start query |
| `limit`\* | u32 | Maximum number of query entries |
| `order_by`\* | OrderBy | Order to make query |

| Key | Description |
| :--- | :--- |
| `Asc` | Make query in ascending order |
| `Desc` | Make query in descending order |

\* = optional

### `VotersResponse`

```rust
#[derive(Serialize, Deserialize, Debug, Clone, PartialEq, JsonSchema)]
pub struct VotersResponse {
    pub voters: Vec<VotersResponseItem>,
}

#[derive(Serialize, Deserialize, Debug, Clone, PartialEq, JsonSchema)]
pub struct VotersResponseItem {
    pub voter: HumanAddr,
    pub vote: VoteOption,
    pub balance: Uint128,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum VoteOption {
    Yes,
    No,
}
```

```javascript
{
  "voters": [
    {
      "voter": "terra1...", 
      "vote": "yes", 
      "balance": "100000000" 
    }, 
    {
      "voter": "terra1...", 
      "vote": "no", 
      "balance": "100000000" 
    } 
  ]
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `voters` | Vec&lt;VotersResponseItem&gt; | List of voter information |

| Key | Type | Description |
| :--- | :--- | :--- |
| `voter` | HumanAddr | Address of voter |
| `vote` | VoteOption | Vote type made by voter |
| `balance` | Uint128 | Amount of staked WHALE locked to vote this poll |

| Key | Description |
| :--- | :--- |
| `yes` | Voter has voted for the proposal |
| `no` | Voter has voted against the proposal |

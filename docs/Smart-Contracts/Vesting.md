# Vesting

The vesting contract handles the WHALE vesting and lockup for the Investor and Team WHALE allocations.

There is an important distiction between the vesting and unlocking schedules. The vesting schedule is the schedule that determines the WHALE token ownership transfers. The unlock schedule determines when the account can claim the tokens of which they have ownership.

## Config & State

See [config and state](https://github.com/White-Whale-Defi-Platform/contracts/blob/audit/v1/contracts/vesting/src/state.rs).

## Messages

See [vesting messages](https://github.com/White-Whale-Defi-Platform/contracts/blob/audit/v1/packages/white_whale/src/vesting.rs).


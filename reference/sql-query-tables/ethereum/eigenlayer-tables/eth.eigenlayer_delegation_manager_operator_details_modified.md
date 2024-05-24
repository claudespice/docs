# eth.eigenlayer.delegation\_manager\_operator\_details\_modified

Decoded table for [OperatorDetailsModified](https://github.com/Layr-Labs/eigenlayer-contracts/blob/mainnet/src/contracts/core/DelegationManager.sol#L473) events from the [DelegationManager](https://etherscan.io/address/0x39053d51b77dc0d36036fc1fcc8cb819df8ef37a) contract on Ethereum mainnet.

Emitted when the operator details are modified.

| Column Name                          | Data Type         | Description                                                  |
| ------------------------------------ | ----------------- | ------------------------------------------------------------ |
| operator                             | CHARACTER VARYING | The identifier of the operator.                              |
| new_operator_earnings_receiver       | CHARACTER VARYING | The address receiving the operator's new earnings.           |
| new_operator_delegation_approver     | CHARACTER VARYING | The address approving the operator's delegation.             |
| new_operator_staker_opt_out_window_blocks | BIGINT            | The number of blocks for the new operator's staker opt-out window. |
| transaction_hash                     | CHARACTER VARYING | The hash of the transaction.                                 |
| log_index                            | BIGINT            | The index of the log within the block.                       |
| block_timestamp                      | BIGINT            | The timestamp of the block.                                  |
| block_number                         | BIGINT            | The number of the block.                                     |
| block_hash                           | CHARACTER VARYING | The hash of the block.                                       |
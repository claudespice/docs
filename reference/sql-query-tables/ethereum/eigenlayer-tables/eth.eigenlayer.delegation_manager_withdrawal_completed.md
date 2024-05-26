# eth.eigenlayer.delegation\_manager\_withdrawal\_completed

Decoded table for [WithdrawalMigrated](https://github.com/Layr-Labs/eigenlayer-contracts/blob/mainnet/src/contracts/core/DelegationManager.sol#L366) events from the [DelegationManager](https://etherscan.io/address/0x39053d51b77dc0d36036fc1fcc8cb819df8ef37a) contract on Ethereum mainnet.

Emitted when a withdrawal is migrated.

| Column Name           | Data Type         | Description                                         |
| --------------------- | ----------------- | --------------------------------------------------- |
| `old_withdrawal_root` | CHARACTER VARYING | The previous root hash of the withdrawal operation. |
| `new_withdrawal_root` | CHARACTER VARYING | The new root hash of the withdrawal operation.      |
| `transaction_hash`    | CHARACTER VARYING | The hash of the transaction.                        |
| `log_index`           | BIGINT            | The index of the log within the block.              |
| `block_timestamp`     | BIGINT            | The timestamp of the block.                         |
| `block_number`        | BIGINT            | The number of the block.                            |
| `block_hash`          | CHARACTER VARYING | The hash of the block.                              |

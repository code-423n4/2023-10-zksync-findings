# GAS OPTIMIZATIONS

| Issue Count | Issues | Instances | Gas Saves |
|----------|----------|----------|----------|
| [G-1]  |Struct can be packed into fewer storage slots| 1  | 2000 |
| [G-2]  |Optimize Boolean States with uint256(1)/uint256(2) Approach ``[Additional Instance]``| 8 | 68400  |





## [G-1] Struct can be packed into fewer storage slots

### Saves ``2000 GAS``

However, if we know that the protocolVersion will never have a value that requires all 256 bits, we can use a smaller data type, like uint96. By doing this, the data requires less space, and other variables can potentially be packed into the same storage slot. This more efficient use of storage slots can reduce the gas cost for certain operations.

The result of this optimization, as mentioned, is a potential gas savings of around 2,000 units.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L136


```diff
FILE: code/contracts/ethereum/contracts/zksync/Storage.sol


79 : struct AppStorage {
80 :     /// @dev Storage of variables needed for deprecated diamond cut facet
81 :     uint256[7] __DEPRECATED_diamondCutStorage;
82 :     /// @notice Address which will exercise critical changes to the Diamond Proxy (upgrades, freezing & unfreezing)
83 :     address governor;
84 :     /// @notice Address that the governor proposed as one that will replace it
85 :     address pendingGovernor;
86 :     /// @notice List of permitted validators
87 :     mapping(address => bool) validators;
88 :     /// @dev Verifier contract. Used to verify aggregated proof for batches
89 :     IVerifier verifier;
90 :     /// @notice Total number of executed batches i.e. batches[totalBatchesExecuted] points at the latest executed batch
91 :     /// (batch 0 is genesis)
92 :     uint256 totalBatchesExecuted;
93 :     /// @notice Total number of proved batches i.e. batches[totalBatchesProved] points at the latest proved batch
94 :     uint256 totalBatchesVerified;
95 :     /// @notice Total number of committed batches i.e. batches[totalBatchesCommitted] points at the latest committed
96 :     /// batch
97 :     uint256 totalBatchesCommitted;
98 :     /// @dev Stored hashed StoredBatch for batch number
99 :     mapping(uint256 => bytes32) storedBatchHashes;
100:     /// @dev Stored root hashes of L2 -> L1 logs
101:     mapping(uint256 => bytes32) l2LogsRootHashes;
102:     /// @dev Container that stores transactions requested from L1
103:     PriorityQueue.Queue priorityQueue;
104:     /// @dev The smart contract that manages the list with permission to call contract functions
105:     IAllowList allowList;
106:     /// @notice Part of the configuration parameters of ZKP circuits. Used as an input for the verifier smart contract
107:     VerifierParams verifierParams;
108:     /// @notice Bytecode hash of bootloader program.
109:     /// @dev Used as an input to zkp-circuit.
110:     bytes32 l2BootloaderBytecodeHash;
111:     /// @notice Bytecode hash of default account (bytecode for EOA).
112:     /// @dev Used as an input to zkp-circuit.
113:     bytes32 l2DefaultAccountBytecodeHash;
114:     /// @dev Indicates that the porter may be touched on L2 transactions.
115:     /// @dev Used as an input to zkp-circuit.
116:     bool zkPorterIsAvailable;
117:     /// @dev The maximum number of the L2 gas that a user can request for L1 -> L2 transactions
118:     /// @dev This is the maximum number of L2 gas that is available for the "body" of the transaction, i.e.
119:     /// without overhead for proving the batch.
120:     uint256 priorityTxMaxGasLimit;
121:     /// @dev Storage of variables needed for upgrade facet
122:     UpgradeStorage __DEPRECATED_upgrades;
123:     /// @dev A mapping L2 batch number => message number => flag.
124:     /// @dev The L2 -> L1 log is sent for every withdrawal, so this mapping is serving as
125:     /// a flag to indicate that the message was already processed.
126:     /// @dev Used to indicate that eth withdrawal was already processed
127:     mapping(uint256 => mapping(uint256 => bool)) isEthWithdrawalFinalized;
128:     /// @dev The most recent withdrawal time and amount reset
129:     uint256 __DEPRECATED_lastWithdrawalLimitReset;
130:     /// @dev The accumulated withdrawn amount during the withdrawal limit window
131:     uint256 __DEPRECATED_withdrawnAmountInWindow;
132:     /// @dev A mapping user address => the total deposited amount by the user
133:     mapping(address => uint256) totalDepositedAmountPerUser;
134:     /// @dev Stores the protocol version. Note, that the protocol version may not only encompass changes to the
135:     /// smart contracts, but also to the node behavior.
- 136:     uint256 protocolVersion;
137:     /// @dev Hash of the system contract upgrade transaction. If 0, then no upgrade transaction needs to be done.
138:     bytes32 l2SystemContractsUpgradeTxHash;
139:     /// @dev Batch number where the upgrade transaction has happened. If 0, then no upgrade transaction has happened
140:     /// yet.
141:     uint256 l2SystemContractsUpgradeBatchNumber;
142:     /// @dev Address which will exercise non-critical changes to the Diamond Proxy (changing validator set & unfreezing)
143:     address admin;
+ 136:     uint96 protocolVersion;
144:     /// @notice Address that the governor or admin proposed as one that will replace admin role
145:     address pendingAdmin;
146: }


```
## [G-2] Optimize Boolean Statesin struct with uint256(1)/uint256(2) Approach [Additional Instance]

### Saves ``68400 GAS``
By adopting uint256(1) for 'true' and uint256(2) for 'false', we can eliminate the Gwarmaccess cost of 100 gas and sidestep the 20,000 gas fee of Gsset when toggling from 'false' back to 'true', especially if it had been set to 'true' earlier.



https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L32

```diff
FILE: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

32. bool isFreezable;

53. bool isFrozen;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L32

```diff

code/contracts/ethereum/contracts/zksync/Storage.sol

32.    bool approvedBySecurityCouncil;

49.    bool isService;

87.    mapping(address => bool) validators;

116.   bool zkPorterIsAvailable;

127.    mapping(uint256 => mapping(uint256 => bool)) isEthWithdrawalFinalized;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L43

```diff

code/contracts/ethereum/contracts/zksync/DiamondInit.sol


43.        bool zkPorterIsAvailable;

```

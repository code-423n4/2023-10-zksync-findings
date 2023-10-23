G-1. No need to initialize variables with default values 

If a variable is not set/initialized, it is assumed to have the default value(0, false, 0x0 etc depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.

Declaring uint256 i = 0; means doing an MSTORE of the value 0 instead you could just declare uint256 i to declare the variable without assigning it's default value, saving 3 gas per declaration.

Instances:

File : contracts/facets/Executor.sol

123 :     for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE))

209 :     for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc())

241 :     for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc())

263 :     for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc())

293 :     for (uint256 i = 0; i < nBatches; i = i.uncheckedInc())

330 :     for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc())

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123


File : contracts/facets/Mailbox.sol

395 :       for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) { 

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L395


File : contracts/libraries/Diamond.sol

100 :        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

138 :        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

159 :        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

179 :        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100


File : contracts/facets/Getters.sol

182 :        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182


File : contracts/libraries/TransactionValidator.sol

92 :        uint256 costForPubdata = 0;

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L92


File : contracts/ValidatorTimelock.sol

85 :            for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116 :            for (uint256 i = 0; i < _newBatchesData.length; ++i) {

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85



G-2. Do not calculate constants to save gas.

Instances :

File : contracts/zksync/Config.sol

14 : uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

26 : uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

33 : uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;

65 : uint256 constant INPUT_MASK = $$(~uint256(0) >> 32);

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L14

https://code4rena.com/reports/2023-08-shell#g-01-do-not-calculate-constants



G-3. Using the indexed keyword for value types such as uint, bool, and address saves gas costs.

If you add indexed keyword, you can save gas at least 80 gas in emitting event.
Up to three `indexed` can be used per event and should be added.
Please read this document : https://gist.github.com/Tomosuke0930/9bf61e01a8c3e214d95a9b84dcb41d97

Instances :

File : contracts/libraries/Diamond.sol

23 :     event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L23

File : contracts/interfaces/IMailbox.sol

142-148 :  event NewPriorityRequest(
        uint256 txId,
        bytes32 txHash,
        uint64 expirationTimestamp,
        L2CanonicalTransaction transaction,
        bytes[] factoryDeps
    );
	
153 :     event EthWithdrawalFinalized(address indexed to, uint256 amount);

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L142

File : contracts/zksync/ValidatorTimelock.sol

27 :     event NewExecutionDelay(uint256 _newExecutionDelay);

30 :     event NewValidator(address _oldValidator, address _newValidator);

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L27

File : contracts/interfaces/IExecutor.sol

106 :     event BlocksRevert(uint256 totalBatchesCommitted, uint256 totalBatchesVerified, uint256 totalBatchesExecuted);

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L106

File : contracts/interfaces/IAdmin.sol

31 :     event IsPorterAvailableStatusUpdate(bool isPorterAvailable);

34 :     event ValidatorStatusUpdate(address indexed validatorAddress, bool isActive);

51 :     event NewPriorityTxMaxGasLimit(uint256 oldPriorityTxMaxGasLimit, uint256 newPriorityTxMaxGasLimit);

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L31









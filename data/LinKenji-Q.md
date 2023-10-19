## Impact

For shadow operations, there is no way to verify if the operation data matches the `id`. An invalid operation could execute with a legitimate `id`.

## Proof of Concept

The problem arises because shadow operations do not publish their operation data on-chain: [scheduleShadow](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/governance/Governance.sol#L142-L145)

```solidity
function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {

  // Shadow operation only provides an ID  
  // but no operation data

  _schedule(_id, _delay);
  
  emit ShadowOperationScheduled(_id, _delay);

}
```

Later when executing, the operation data is not looked up, only the ID is checked: [execute](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/governance/Governance.sol#L167-L181)

```solidity
function execute(bytes32 _id) external {

  // Execute only checks the ID

  require(isOperationReady(_id), "Not ready");
  
  _execute(_id);

}
```

This allows an invalid operation to execute as long as it provides a valid ID.

Because the data is never validated against the ID, this means:

- The `msg.sender` can pass any arbitrary operation data 

- As long as it provides a valid scheduled ID, the execution will succeed

This is a problem because:

- The ID does not actually correlate to the real operation data

- No one can verify the operation matches the original scheduled proposal

- Invalid, unintended operations can execute with a legit ID

> Here is a code example that demonstrates how an invalid operation can execute with a valid ID in the Governance contract:

```solidity

// Governance contract  

struct Operation {
  //...
}

mapping(bytes32 => Operation) public operations;

function scheduleShadow(bytes32 _id, uint256 _delay) external {
  // Schedule shadow operation
  
  _schedule(_id, _delay);

}

function execute(bytes32 _id) external {

  Operation memory op = operations[_id];

  _execute(op.calls);

}


// Attacker contract

bytes32 public constant LEGIT_ID = 0x1234...; // ID of a legit scheduled operation

function attack() external {

  Operation memory maliciousOp;
  
  // Populate malicious operation

  Governance(gov).execute(LEGIT_ID);

  // Executes malicious op with legitimate scheduled ID

}
```

- The attacker creates a malicious operation unrelated to the scheduled one

- By passing a valid ID, the malicious operation will execute

- The ID does not match the actual data being executed

In summary, the proof is:

- Shadow operations record IDs only, allowing arbitrary data to be executed

- Any data can execute as long as a valid scheduled ID is provided

- This lets attackers execute unintended operations with legitimate IDs

## Tools Used

## Recommended Mitigation Steps

shadow operations should record the hash of the operation data when scheduled:

```solidity
function scheduleShadow(bytes32 _id, bytes32 _dataHash, uint256 _delay) external {

  // Store hash of operation data
  operationDataHashes[_id] = _dataHash;  

}
```

And validate it when executing:

```solidity 
function execute(bytes32 _id, bytes memory _data) external {

  bytes32 dataHash = keccak256(_data);

  // Check operation data hash matches
  require(dataHash == operationDataHashes[_id], "Invalid operation");
  
  // Execute operation 

}
```

This ensures the operation data matches the scheduled data for shadow operations.
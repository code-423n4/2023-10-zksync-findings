## Impact

No events for critical operations like batch commits, executions, etc. Makes monitoring and debugging difficult.

## Proof of Concept

There are no events emitted for critical operations like batch commits and executions.

Some examples:

`commitBatches` function https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L177-L200

```solidity
    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
        external
        override
        nonReentrant
        onlyValidator
    {
// no events emitted  

```

Similarly, `executeBatches` on https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L291-L296

```solidity
 function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
   // no events emitted
 } 
```
This makes it difficult to monitor key operations happening in the contract. 


Critical operations like batch commits and executions are opaque to off-chain monitoring.

## Recommended Mitigation Steps

I would recommend emitting events from these functions on batch commits and executions.

For example:

```solidity
event BatchCommitted(
  uint256 indexed batchNumber,
  bytes32 indexed batchHash
);

event BatchExecuted(
  uint256 indexed batchNumber, 
  bytes32 indexed batchHash
);

function commitBatches() {
  ...

  emit BatchCommitted(batchNumber, batchHash);
}

function executeBatches() {
  ...
  
  emit BatchExecuted(batchNumber, batchHash);
}
```


------------
------------

## Impact

There is no limit on the number of calls that can be batched in a single operation. This could allow the owner to execute a large number of critical calls in one operation.

## Proof of Concept


`_execute()` function allows batching an unlimited number of external calls into a single operation. This is problematic because it allows the contract owner to bundle critical privileged actions in a way that makes them difficult to evaluate individually. 

For example, the owner could create an operation that:

1. Set a new owner address
2. Transfers all funds to the new owner 
3. Removes access for all other roles
4. Distributes critical NFTs to the new owner
5. Etc...

All of these privileged operations could be executed at once, with no way for the security council or users to intervene in between steps. 

This bypasses the intended security benefit of requiring a timelock or security council approval for sensitive operations. It allows the owner to bundle exploits before they can be detected and stopped.

The impact is essentially giving the owner a single transaction that carries the combined authority of the entire governance system. This undermines all other access controls and defeat's the contract's security model.

It violates the principle of least authority by granting the owner capabilities far beyond their intended role.

The concern around unlimited batching of calls in a single operation comes from [#L224-L230](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L224-L231)

```js
function _execute(Call[] calldata _calls) internal {
```

The `_execute` function takes an array of Call structs as input. There is no limit set on the length of this array. This allows the owner to pass a very large array and batch an unlimited number of external calls in a single operation. 

Here is a test that demonstrates exploiting the unbounded batch execution.

```js
// Contracts
const Governance = await ethers.getContractFactory("Governance");
const Exploit = await ethers.getContractFactory("Exploit");

// Tests 
it("Executes unbounded batches", async () => {

  const governance = await Governance.deploy();
  const exploit = await Exploit.deploy(governance.address);

  // Execute batch of 10 calls
  await exploit.exploit();

  // Check all calls executed
  for(let i = 0; i < 10; i++) {
    expect(
      await governance.wasCallExecuted(i)  
    ).to.be.true;
  }

});

// Governance contract
contract Governance {

  bool[10] public wasCallExecuted;

  function execute(Call[10] calldata calls) external {
    for(uint i = 0; i < calls.length; i++) {
      wasCallExecuted[i] = true;    
    }
  }

}

// Exploit contract 
contract Exploit {

  Governance governance;

  constructor(address governanceAddress) {
    governance = Governance(governanceAddress); 
  }

  function exploit() external {
    Call[10] memory calls;

    governance.execute(calls);
  }

}
```


## Recommended Mitigation Steps

Set a reasonable limit on the maximum length of the `_calls` array.

For example:

```js
function _execute(Call[10] calldata _calls) internal { 
```
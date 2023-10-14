## [L-01] Massive number lengths
The SystemContext contract has a very large number in length.
The number is 2500000000000000 within line 44.
**Location**
**Bad**
```sol
// system-contracts/contracts/SystemContext.sol#L44-L44
uint256 public difficulty = 2500000000000000;
```
**Fix**
```sol
// system-contracts/contracts/SystemContext.sol#L44-L44
uint256 public difficulty = 25e14;
```
**Remediation**
Use the scientific notation value of 25e14 instead.

## [L-02] The loop contains msg.value
**Description**
The msg.value being utilised within a loop is incorrect programming logic.
The resulting vulnerability is the same ether utilised incrementally in a loop.
**Location**
```sol
// system-contracts/contracts/ContractDeployer.sol#L253-L255
 for (uint256 i = 0; i < deploymentsLength; ++i) {
            this.forceDeployOnAddress{value: _deployments[i].value}(_deployments[i], msg.sender);
        }
```
**Mitigation**
Make sure the msg.value in the loop is contained in this function only and it not linked to any other functions.

## [L-03] Missing events on the following contracts
**Description**
Events are inheritable members of contracts. When you call them, they cause the arguments to be stored in the transaction’s log — a special data structure in the blockchain.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract L2ERC20Bridge was found to be missing these events on the function _deployL2Token which would make it difficult or impossible to track these transactions off-chain.
## [L-04] Event based reentrancy
**Description**
When Event-based Re-entrancy happens, events get printed in the event of an external call which leads to other legitimate event calls not being printed or emitted.  This makes it difficult or impossible to track these transactions off-chain.
**Vulnerable functions to event-based re-entrancy are**
```sol
// zksync/contracts/bridge/L2ERC20Bridge.sol#L63-L88
function finalizeDeposit(
        address _l1Sender,
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        bytes calldata _data
    ) external payable override {
        // Only the L1 bridge counterpart can initiate and finalize the deposit.
        require(AddressAliasHelper.undoL1ToL2Alias(msg.sender) == l1Bridge, "mq");
        // The passed value should be 0 for ERC20 bridge.
        require(msg.value == 0, "Value should be 0 for ERC20 bridge");
        address expectedL2Token = l2TokenAddress(_l1Token);
        address currentL1Token = l1TokenAddress[expectedL2Token];
        if (currentL1Token == address(0)) {
            address deployedToken = _deployL2Token(_l1Token, _data);
            require(deployedToken == expectedL2Token, "mt");
            l1TokenAddress[expectedL2Token] = _l1Token;
        } else {
            require(currentL1Token == _l1Token, "gg"); // Double check that the expected value equal to real one
        }
        IL2StandardToken(expectedL2Token).bridgeMint(_l2Receiver, _amount);
        emit FinalizeDeposit(_l1Sender, _l2Receiver, expectedL2Token, _amount);
    }
```
```sol
// zksync/contracts/bridge/L2ERC20Bridge.sol#L105-L119
function withdraw(
        address _l1Receiver,
        address _l2Token,
        uint256 _amount
    ) external override {
        IL2StandardToken(_l2Token).bridgeBurn(msg.sender, _amount);
        address l1Token = l1TokenAddress[_l2Token];
        require(l1Token != address(0), "yh");
        bytes memory message = _getL1WithdrawMessage(_l1Receiver, l1Token, _amount);
        L2ContractHelper.sendMessageToL1(message);
        emit WithdrawalInitiated(msg.sender, _l1Receiver, _l2Token, _amount);
    }
```
```sol
// zksync/contracts/bridge/L2Weth.sol#L78-L85
function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
        _burn(_from, _amount);
        // sends Ether to the bridge
        (bool success, ) = msg.sender.call{value: _amount}("");
        require(success, "Failed withdrawal");
        emit BridgeBurn(_from, _amount);
    }
```
```sol
// zksync/contracts/bridge/L2WethBridge.sol#L63-L81
function withdraw(
        address _l1Receiver,
        address _l2Token,
        uint256 _amount
    ) external override {
        require(_l2Token == l2WethAddress, "Only WETH can be withdrawn");
        require(_amount > 0, "Amount cannot be zero");
        // Burn WETH on L2, receive ETH.
        IL2StandardToken(l2WethAddress).bridgeBurn(msg.sender, _amount)
        // WETH withdrawal message.
        bytes memory wethMessage = abi.encodePacked(_l1Receiver);
        // Withdraw ETH to L1 bridge.
        L2_ETH_ADDRESS.withdrawWithMessage{value: _amount}(l1Bridge, wethMessage);
        emit WithdrawalInitiated(msg.sender, _l1Receiver, l2WethAddress, _amount);
    }
```
```sol
// zksync/contracts/bridge/L2WethBridge.sol#L88-L107
function finalizeDeposit(
        address _l1Sender,
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        bytes calldata // _data
    ) external payable override {
        require(
            AddressAliasHelper.undoL1ToL2Alias(msg.sender) == l1Bridge,
            "Only L1 WETH bridge can call this function"
        );
        require(_l1Token == l1WethAddress, "Only WETH can be deposited");
        require(msg.value == _amount, "Amount mismatch");
        // Deposit WETH to L2 receiver.
        IL2Weth(l2WethAddress).depositTo{value: msg.value}(_l2Receiver);
        emit FinalizeDeposit(_l1Sender, _l2Receiver, l2WethAddress, _amount);
    }
```
## 1. `calldata` VARIABLE CAN BE USED INPLACE OF `memory` VARIABLE IN THE `L1ERC20Bridge.finalizeWithdrawal` FUNCTION TO SAVE GAS

The `L1ERC20Bridge.finalizeWithdrawal` function is used to finalize the withdrawal and release funds. The function calls the `_parseL2WithdrawalMessage` internal function to parse the L2 withdraw data (`_message`) into required data fields as shown below:

        (address l1Receiver, address l1Token, uint256 amount) = _parseL2WithdrawalMessage(l2ToL1Message.data);

The above function call uses the `l2ToL1Message.data` variable which is read from the `memory`. But this same variable `l2ToL1Message.data = _message` and the `_message` is `calldata`.

Hence one `MLOAD` operation can be called by using the `_message` `calldata` variable in place of the `l2ToL1Message.data` memory variable when calling the `_parseL2WithdrawalMessage` function. The modified code snippet is shown below:

        (address l1Receiver, address l1Token, uint256 amount) = _parseL2WithdrawalMessage(_message);

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L308

## 2. STATE VARIABLE CAN BE REPLACED WITH A CALLDATA VARIABLE TO SAVE GAS IN THE `L1WethBridge.deposit` FUNCTION

The `L1WethBridge.deposit` function is used to initiate a WETH deposit by depositing WETH into the L1 bridge contract and unwrapping it to ETH. The function constructs the `L2 calldata for the deposit` as shown below:

        bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, l1WethAddress, _amount);

The issue here is that the `deposit` function is using the `l1WethAddress` state variable for the function call where as the `_l1Token` calldata variable could have been used for the `_getDepositL2Calldata` function call since the `_l1Token == l1WethAddress`. 

It is recommended to use the `_l1Token` in place of the `l1WethAddress` state variable in the `_getDepositL2Calldata` function call inside the `L1WethBridge.deposit` function which will save an extra `SLOAD` operation.

The modified line of code will look as follows:

        bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, _l1Token, _amount);

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L176

## 3. REDUNDANT DECODING OPERATIONS PERFORMED INSIDE THE `L1WethBridge._parseL2EthWithdrawalMessage` FUNCTION CAN BE OMITTED TO SAVE GAS

The `L1WethBridge._parseL2EthWithdrawalMessage` function is used to parse the ETH withdraw message with additional data about WETH withdrawal, that came from L2EthToken contract. The `_parseL2EthWithdrawalMessage` function decodes each of the data fields in the passed in `_message` input bytes array, in order. 

But only the `l1WethReceiver` address and `ethAmount` variables of the decoded variables are returned back to the calling function as shown below:

    function _parseL2EthWithdrawalMessage(
        bytes memory _message
    ) internal view returns (address l1WethReceiver, uint256 ethAmount) {

Hence all the other decoded data are memory variables which will be destroyed after the function execution finishes and hence they are not used during the function execution.

Hence it is recommended to pass the `offset as 24` manually to the `UnsafeBytes.readUint256(_message, offset)` function call to retrieve the `ethAmount` and pass the `offset as 76` manually to the `UnsafeBytes.readAddress(_message, offset)` function call to retrieve the `l1WethReceiver` address. 

This will ensure redundant operations performed to decode other unused variable will be omitted. Hence this will save gas spent on those redundant decoding operations inside the `L1WethBridge._parseL2EthWithdrawalMessage` function.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L272-L274
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L291
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L298
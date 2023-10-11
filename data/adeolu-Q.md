## [L-01] - bridigng token from L2 to L1 may cause funds/value to be stuck on L1 bridge if L1 receviver is addr(0) as many L1 erc20's prevent transfers to addr(0). 

when using the `withdraw()` fcn in `L2ERC20Bridge.sol`, the `_l1Receiver` receiver can be set to address(0) and l2 standard tokens will get burned as usual and the l2 message will be sent to the L1 bridge. 
```
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

when message gets to L1 bridge, `finaliseWithdrawal()` is called and it will validate the message and then try to send the token to the `_l1Receiver` which is address(0). As most tokens (especially tokens that implement the openzeppelin erc20 token contract, see [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/793d92a3331538d126033cbacb1ee5b8a7d95adc/contracts/token/ERC20/ERC20.sol#L175) ) prevent transfers to address(0), this will mean that the function will revert and there will be a value discrepancy between both bridges. Value will be burnt on L2 but not redeemable/stuck on L1 bridge. 
```
    function finalizeWithdrawal(
        uint256 _l2BatchNumber,
        uint256 _l2MessageIndex,
        uint16 _l2TxNumberInBatch,
        bytes calldata _message,
        bytes32[] calldata _merkleProof
    ) external nonReentrant senderCanCallFunction(allowList) {
        require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "pw");

        L2Message memory l2ToL1Message = L2Message({
            txNumberInBatch: _l2TxNumberInBatch,
            sender: l2Bridge,
            data: _message
        });

        (address l1Receiver, address l1Token, uint256 amount) = _parseL2WithdrawalMessage(l2ToL1Message.data);
        // Preventing the stack too deep error
        {
            bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
            require(success, "nq");
        }

        isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;
        // Withdraw funds
        IERC20(l1Token).safeTransfer(l1Receiver, amount);

        emit WithdrawalFinalized(l1Receiver, l1Token, amount);
    }
```

## Recommended mitigation
prevent this by adding a check that reverts if `_l1Receiver` is address(0) in function `withdraw()` of `L2ERC20Bridge.sol`

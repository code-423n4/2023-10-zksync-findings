Although L2 refund is done on batch basis, more transaction level function calls can be combined to batch basis to save gas.

As one example, 
 `let payToOperator := safeMul(gasPrice, safeSub(gasLimit, refundGas, "lpah"), "mnk")` is called inside of `processL1Tx`, which is inside of `processTx`, which is inside of the batch loop. `safeMul` with gasPrice can be calculated and `mintEther(BOOTLOADER_FORMAL_ADDR(), payToOperator, false)` can be called outside of the batch loop.
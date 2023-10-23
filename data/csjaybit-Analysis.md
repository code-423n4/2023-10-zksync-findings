[S-01] It is suggested to write a separate errors.sol file bootloader.yul

In https://github.com/code-423n4/2023-10-zksync/blob/main/bot-report.md writing descriptive errors is suggested solution for short errors in https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul

however, bootloader.yul is written in pure yul, writing short errors in yul files is more efficient, long errors in yul files can cause more gas consumption, which doesn't sounds more efficient for system contracts like bootloader.yul , thus writing separate errors.sol file is highly suggested for bootloader.yul where meaning of short errors is explained


[S-02] There should be a visual layout for bootloader.yul in docs or drawn in separate layout.sol


https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul is very large solidit file ~4000 SLOC, there should be visual layout for bootloader.yul for better readability.

For example, I was checking function getBaseFee() I see safeMul function at line 42
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L42C15-L42C15

now I try to find safeMul by searching I have to write whole function's name, while function safemul is located at bottom of bootloader.yul, https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L3226
Using tool for generating callgraph doesn't resolves it, cause safemul is called multiple times in bootloader.yul, having a visual layout can be really helpful for auditors and developers to understand and digest large smartcontracts.

### Time spent:
50 hours
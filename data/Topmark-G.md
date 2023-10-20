### Report 1:
Using expected result of calculations from constant values would save gas instead of carrying out the calculation at every function call in the L1Messenger contract.
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L51-L60
```solidity
 /// The gas cost of processing one keccak256 round.
    uint256 internal constant KECCAK_ROUND_GAS_COST = 40;

    /// The number of bytes processed in one keccak256 round.
    uint256 internal constant KECCAK_ROUND_NUMBER_OF_BYTES = 136;

    /// The gas cost of calculation of keccak256 of bytes array of such length.
    function keccakGasCost(uint256 _length) internal pure returns (uint256) {
        return KECCAK_ROUND_GAS_COST * (_length / KECCAK_ROUND_NUMBER_OF_BYTES + 1);
    }
```
The code above shows how KeccakGasCost is calculated, the only variable that is not constant is the "_length" parameter
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L93
```solidity
 function sendL2ToL1Log(
        ...
    ) external onlyCallFromSystemContract returns (uint256 logIdInMerkleTree) {
        ...
---    uint256 gasToPay = keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) + 3 * keccakGasCost(64);
---        SystemContractHelper.burnGas(Utils.safeCastToU32(gasToPay));

+++        SystemContractHelper.burnGas(Utils.safeCastToU32(160));
```
The code above shows how keccakGasCost(...) function is called to calculate "gasToPay" both calls are also using constant values, "L2_TO_L1_LOG_SERIALIZE_SIZE = 88" from [Config.sol contract](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L11) and "64" for the second call, this shows the expected output can be accurately calculated and the value used without the need to call the function over and over again using gas.
The value of uint256 gasTopay would equal 40 + 3 * 40 = 160.
160 can be easily used directly as corrected in the code above.
Note: A detailed comment description should be provided instead to show how "gasToPay" was calculated as 160.
###  Report 2:

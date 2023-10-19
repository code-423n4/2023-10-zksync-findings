
# These issues is not listed in the automated findings report.
## Shadowing using local variable

	In hashL2Bytecode function, "bytecodeLenInWords" local variable name is confusing with "bytecodeLenInWords" internal function.
	Please rename this local variable name.

###### "bytecodeLenInWords" local variable
	https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L87-L88

###### "bytecodeLenInWords" internal function
	https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L44-L48	

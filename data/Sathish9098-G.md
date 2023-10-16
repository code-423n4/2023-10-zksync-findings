# GAS OPTIMIZATIONS
																																																																																																																													##

TRUE	Structs can be packed into fewer storage slots	Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings																								
																										
			/// @audit Variable ordering with 2 slots instead of the current 3:																							
			///           bytes(32):bytecode, address(20):toft, bool(1):revertOnFailure																							
			27        struct ExecutionCall {																							
			28            address toft;																							
			29            bytes bytecode;																							
			30            bool revertOnFailure;																							
			31:       }

## [G-] State variables should be cached in stack variables rather than re-reading them from storage	The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

##

## [G-] The result of function calls should be cached rather than re-calling the function	

The instances below point to the second+ call of the function within a single function																								
																										
																										
			/// @audit market.oracle() on line 918																							
			930:          (, info.oracleExchangeRate) = IOracle(market.oracle()).peek(																							
			/// @audit market.oracleData() on line 919																							
			931:              market.oracleData()																							
			/// @audit market.oracle() on line 918																							
			933:          info.spotExchangeRate = IOracle(market.oracle()).peekSpot(																							
			/// @audit market.oracleData() on line 919																							
			934:              market.oracleData()																							
									

##

## [G-] Optimize Gas Usage by Avoiding Variable Declarations Inside Loops

The variables ``action``, ``facet``, ``isFacetFreezable``, and ``selectors`` are all declared inside the loop. This means that a new instance of each variable will be created for each iteration of the loop. Saves 200-300 Gas per iteration

```solidity

for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
            action = facetCuts[i].action;
             facet = facetCuts[i].facet;
            isFacetFreezable = facetCuts[i].isFreezable;
             selectors = facetCuts[i].selectors;

            require(selectors.length > 0, "B"); // no functions for diamond cut

            if (action == Action.Add) {
                _addFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Replace) {
                _replaceFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Remove) {
                _removeFunctions(facet, selectors);
            } else {
                revert("C"); // undefined diamond cut action
            }
        }


```

```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
index 0b1fbdc..2f3cf9e 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -97,11 +97,15 @@ library Diamond {
         address initAddress = _diamondCut.initAddress;
         bytes memory initCalldata = _diamondCut.initCalldata;
         uint256 facetCutsLength = facetCuts.length;
+        Action action;
+        address facet;
+      bool isFacetFreezable;
+    bytes4[] memory selectors;
         for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
-            Action action = facetCuts[i].action;
+            action = facetCuts[i].action;
-            address facet = facetCuts[i].facet;
+             facet = facetCuts[i].facet;
-            bool isFacetFreezable = facetCuts[i].isFreezable;
+            isFacetFreezable = facetCuts[i].isFreezable;
-            bytes4[] memory selectors = facetCuts[i].selectors;
+            selectors = facetCuts[i].selectors;

```


##

## [G-] Remove or replace unused state variables

Saves a storage slot. If the variable is assigned a non-zero value, saves Gsset (20000 gas). If it's assigned a zero value, saves Gsreset (2900 gas). If the variable remains unassigned, there is no gas savings unless the variable is public, in which case the compiler-generated non-payable getter deployment cost is saved. If the state variable is overriding an interface's public function, mark the variable as constant or immutable so that it does not use a storage slot.

```solidity
FILE: 

54: mapping(address => uint256) public __DEPRECATED_lastWithdrawalLimitReset;

57: mapping(address => uint256) public __DEPRECATED_withdrawnAmountInWindow;

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L54-L57	

##

## [G-] Code optimization for reduced gas consumption



 


## [G-] Optimize code to avoid extra GAS

### Any revert inside the for loop then caching ``initAddress`` ,``initCalldata `` is waste of the computation and gas . Saves ``300 GAS``. Values of ``initAddress`` and ``initCalldata `` is used after the for loop. So caching values before for loop is unnecessary.

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L97-L98

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

function diamondCut(DiamondCutData memory _diamondCut) internal {
        FacetCut[] memory facetCuts = _diamondCut.facetCuts;
-        address initAddress = _diamondCut.initAddress;
-        bytes memory initCalldata = _diamondCut.initCalldata;
        uint256 facetCutsLength = facetCuts.length;
        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
            Action action = facetCuts[i].action;
            address facet = facetCuts[i].facet;
            bool isFacetFreezable = facetCuts[i].isFreezable;
            bytes4[] memory selectors = facetCuts[i].selectors;

            require(selectors.length > 0, "B"); // no functions for diamond cut

            if (action == Action.Add) {
                _addFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Replace) {
                _replaceFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Remove) {
                _removeFunctions(facet, selectors);
            } else {
                revert("C"); // undefined diamond cut action
            }
        }
+        address initAddress = _diamondCut.initAddress;
+        bytes memory initCalldata = _diamondCut.initCalldata;

        _initializeDiamondCut(initAddress, initCalldata);
        emit DiamondCut(facetCuts, initAddress, initCalldata);
    }

```


### Any revert in  ``block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp`` check then caching lastL2BlockTimestamp value unnecessary and waste of gas 

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L87


```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

  function _verifyBatchTimestamp(
        uint256 _packedBatchAndL2BlockTimestamp,
        uint256 _expectedBatchTimestamp,
        uint256 _previousBatchTimestamp
    ) internal view {
        // Check that the timestamp that came from the system context is expected
        uint256 batchTimestamp = _packedBatchAndL2BlockTimestamp >> 128;
        require(batchTimestamp == _expectedBatchTimestamp, "tb");

        // While the fact that _previousBatchTimestamp < batchTimestamp is already checked on L2,
        // we double check it here for clarity
        require(_previousBatchTimestamp < batchTimestamp, "h3");

-        uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;

        // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BatchTimestamp].
        // So here we need to only double check that:
        // - The timestamp of the batch is not too small.
        // - The timestamp of the last L2 block is not too big.
        require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small
+        uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;
        require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big
    }

```

##

## [G-] Bytes constants are more efficient than string constants

bytes constants are more gas efficient than string constants when the string length is fixed. This is because the Ethereum Virtual Machine (EVM) stores bytes constants in a more compact way than string constants.

As per remix [gas tests](https://gist.github.com/sathishpic22/244dd5b1a18d1dd3aab64bf6304c1cd4) saves ``31630 GAS`` per instance

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

- 22: string public constant override getName = "ExecutorFacet";
+ 22: bytes32 public constant override getName = "ExecutorFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

- 41:  string public constant override getName = "MailboxFacet";
+ 41:  bytes32 public constant override getName = "MailboxFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Getters.sol

- 19: string public constant override getName = "GettersFacet";
+ 19: bytes32 public constant override getName = "GettersFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

- 24:  string public constant override getName = "ValidatorTimelock";
+ 24:  bytes32 public constant override getName = "ValidatorTimelock";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Admin.sol

- 15: string public constant override getName = "AdminFacet";
+ 15: bytes32 public constant override getName = "AdminFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15

##

## [G-] Don't cache variable once used once 

If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the 3 gas the extra stack assignment would spend

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

-  uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;

        // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BatchTimestamp].
        // So here we need to only double check that:
        // - The timestamp of the batch is not too small.
        // - The timestamp of the last L2 block is not too big.
        require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small
-        require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big
+        require(_packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L87-L94

##

## [G-] Use uint256(1)/uint256(2) instead of true/false to save gas for changes

#### Note this instances are not found by bot 

Avoids a Gsset (20000 gas) when changing from false to true, after having been true in the past. Since most of the bools aren't changed twice in one transaction, I've counted the amount of gas as half of the full amount, for each variable.

Also these variables are used very after so i reported that . This saves so much gas 

```solidity
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/Storage.sol

32:  bool approvedBySecurityCouncil;
 
49: bool isService;

87: mapping(address => bool) validators;

116: bool zkPorterIsAvailable;

127: mapping(uint256 => mapping(uint256 => bool)) isEthWithdrawalFinalized;

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L87
																							
##																																																																																																																																																## [G-] Using private rather than public for constants, saves gas	

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a [tuple of the values](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table																								
																										
			File: tapioca-bar-audit/contracts/Penrose.sol																							
			45:       uint256 public immutable tapAssetId;																							
			53:       uint256 public immutable wethAssetId;																							
																										
																										
			41:       uint256 public constant INITIAL_SUPPLY = 46_686_595 * 1e18; // Everything minus DSO																							
			49:       uint256 public constant WEEK = 604800;																							
			53:       uint256 public immutable emissionsStartTime;	


FALSE	Don't compare boolean expressions to boolean literals	if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)																								
			175:              isSingularityMasterContractRegistered[mc] == true,																							
			183:              isBigBangMasterContractRegistered[mc] == true,																							
			322:              isSingularityMasterContractRegistered[mcAddress] == false,																							
			344:              isBigBangMasterContractRegistered[mcAddress] == false,																							
																										
FALSE	Multiple if-statements with mutually-exclusive conditions should be changed to if-else statements	If two conditions are the same, their blocks should be combined																								
			264           if (borrowPartDecimals > 18) {																							
			265               borrowPartScaled = borrowPart / (10 ** (borrowPartDecimals - 18));																							
			266           }																							
			267           if (borrowPartDecimals < 18) {																							
			268               borrowPartScaled = borrowPart * (10 ** (18 - borrowPartDecimals));																							
			269:          }																							
			272           if (collateralPartDecimals > 18) {																							
			273               collateralPartInAssetScaled =																							
			274                   collateralPartInAsset /																							
			275                   (10 ** (collateralPartDecimals - 18));																							
			276           }	




																								
																																																																												
																										
FALSE	Not using the named return variables anywhere in the function is confusing	Consider changing the variable to be an unnamed one, since the variable is never assigned, nor is it returned by name. If the optimizer is not turned on, leaving the code as it is will also waste gas for the stack variable.																								
																										
			/// @audit amount																							
			76        function getCollateralAmountForShare(																							
			77            IMarket market,																							
			78            uint256 share																							
			79:       ) public view returns (uint256 amount) {																							
			/// @audit collateralShares																							
			89        function getCollateralSharesForBorrowPart(																							
			90            IMarket market,																							
			91            uint256 borrowPart,																							
			92            uint256 liquidationMultiplierPrecision,																							
			93            uint256 exchangeRatePrecision																							
			94:       ) public view returns (uint256 collateralShares) {	

FALSE	String literals passed to abi.encode()/abi.encodePacked() should not be split by commas	String literals can be split into multiple parts and still be considered as a single string literal. Adding commas between each chunk makes it no longer a single string, and instead multiple strings. EACH new comma costs [21 gas](https://gist.github.com/IllIllI000/837d1b36c16c9bfe1010f9f775a09bbf) due to stack operations and separate MSTOREs																								
																										
			61:                   return string(abi.encodePacked("ERC1155", " (", asset.strategy.name(), ")"));																							
	https://github.com/code-423n4/2023-09-centrifuge/blob/main/bot-report.md																									

FALSE	Don't use _msgSender() if not supporting EIP-2771	Use msg.sender if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support	

FALSE	Use += for mappings	Using += for mappings saves [40 gas](https://gist.github.com/IllIllI000/4fc5f83a9edc6ed16677258bf58f32a5) due to not having to recalculate the mapping's value's hash																								
																										
	165:             balanceOf[to] = balanceOf[to] + value; // note: we don't need an overflow check here b/c balanceOf[to] <= totalSupply and there is an overflow check below	

Avoid emitting constants

A log topic (declared with indexed) has a gas cost of Glogtopic (375 gas). The Stake and Withdraw events’ second indexed parameter is a constant for a majority of events emitted (with the exception of the events emitted in the _stakeLP() and _withdrawLP() functions) and is unecessary to emit since the value will never change. Alternatively, you can avoid incurring the Glogtopic (375 gas) per call to any function that emits Stake/Withdraw (with the exception of _stakeLP() and _withdrawLP()) by creating separate events for each staking/withdraw function and opt out of using the current indexed asset topic in each event. This way you can still query the different staking/withdraw events and will save 375 gas for each staking/withdraw function (with the exception of _stakeLP() and _withdrawLP()).

Note that the events emitted in the _stakeLP() and withdrawLP() functions are not considered for this issue since the second indexed parameter is for the LP storage variable, which can be changed via the configureLP() function.

Is this possible to cache any external function calls inside the loop

Is this any possible to refatore the code and save gas ?

Is this possible to avoid internal functions?

if any return functions functions first return less gas value lastly return most gas values

Only cache memory or storage inside the condition checks if possible 





																																																																					
																																																																																																																			
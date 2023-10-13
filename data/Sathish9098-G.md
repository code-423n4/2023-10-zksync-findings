# GAS OPTIMIZATIONS
																																																																																																																													##

## [G-] State variables should be cached in stack variables rather than re-reading them from storage	The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

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

## [G-] Redundant cache 

### Any revert inside the for loop then caching ``initAddress`` ,``initCalldata `` is waste of the computation and gas . Saves ``300 GAS``. Values of ``initAddress`` and ``initCalldata `` is used after the for loop. So caching values before for loop is unecessary.

```diff
FILE: 

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
																							
			

TRUE	Structs can be packed into fewer storage slots	Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings																								
																										
			/// @audit Variable ordering with 2 slots instead of the current 3:																							
			///           bytes(32):bytecode, address(20):toft, bool(1):revertOnFailure																							
			27        struct ExecutionCall {																							
			28            address toft;																							
			29            bytes bytecode;																							
			30            bool revertOnFailure;																							
			31:       }

TRUE	Multiple accesses of a mapping/array should use a local variable cache	The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata																								
																										
			/// @audit activeSingularities[_singularity] on line 180																							
			187:          activeSingularities[_singularity].totalDeposited += _amount;																							
			/// @audit activeSingularities[_singularity] on line 221																							
			247:          activeSingularities[_singularity].totalDeposited -= lockPosition.amount;																							
			/// @audit activeSingularities[singularity] on line 264																							
			267:          activeSingularities[singularity].poolWeight = weight;																							
			/// @audit activeSingularities[singularity] on line 283																							
			287:          activeSingularities[singularity].sglAssetID = assetID;																							
			/// @audit activeSingularities[singularity] on line 287																							
			288:          activeSingularities[singularity].poolWeight = weight > 0 ? weight : 1;																							

TRUE	Use uint256(1)/uint256(2) instead of true/false to save gas for changes	Avoids a Gsset (20000 gas) when changing from false to true, after having been true in the past																								
																										
	23:      bool public paused = false;	


TRUE	State variables only set in their definitions should be declared `constant`	Avoids a Gsset (**20000 gas**) at deployment, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmacces - **100 gas**) with a `PUSH32` (**3 gas**).																								
																										
	39:      uint256 public blockGasLimit = type(uint32).max;																									
	43:      address public coinbase = BOOTLOADER_FORMAL_ADDRESS;																									
	46:      uint256 public difficulty = 2500000000000000;

																																																																																														

FALSE	Using private rather than public for constants, saves gas	If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a [tuple of the values](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table																								
																										
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


FALSE	Empty blocks should be removed or emit something	The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if (x) {...} else if (y) {...} else {...} => if (!x) { if (y) {...} else {...} }). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.																								
			267:                  {} catch Error(string memory reason) {																							
			282:                  {} catch Error(string memory reason) {																							
			298:                  {} catch Error(string memory reason) {																							
																										
FALSE	Superfluous event fields	block.timestamp and block.number are added to event information by default so adding them manually wastes gas																								
			138:      event ProtocolWithdrawal(IMarket[] markets, uint256 timestamp);																							

FALSE	Inverting the condition of an if-else-statement wastes gas	Flipping the true and false blocks instead saves [3 gas](https://gist.github.com/IllIllI000/44da6fbe9d12b9ab21af82f14add56b9)																								
			190           if (!_linked) {																							
			191               TapiocaOFT toft = TapiocaOFT(																							
			192                   payable(																							
			193                       Create2.deploy(																							
			194                           0,																							
			195                           keccak256(																							
			196                               abi.encodePacked(																							
			197                                   keccak256(_bytecode),																							
			198                                   address(this),																							
			199                                   _erc20,																							
			200                                   _salt																							
			201                               )																							
			202                           ),																							
			203                           _bytecode																							
			204                       )																							
			205                   )																							
			206               );																							
			207               oft = address(toft);																							
			208           } else {																							
			209               mTapiocaOFT toft = mTapiocaOFT(																							
			210                   payable(																							
			211                       Create2.deploy(																							
			212                           0,																							
			213                           keccak256(																							
			214                               abi.encodePacked(																							
			215                                   keccak256(_bytecode),																							
			216                                   address(this),																							
			217                                   _erc20,																							
			218                                   _salt																							
			219                               )																							
			220                           ),																							
			221                           _bytecode																							
			222                       )																							
			223                   )																							
			224               );																							
			225               oft = address(toft);																							
			226:          }	

FALSE	Consider using bytes32 rather than a string	Using the bytes types for fixed-length strings is more efficient than having the EVM have to incur the overhead of string processing. Consider whether the value needs to be a string. A good reason to keep it as a string would be if the variable is defined in an interface that this project does not own.																								
																										
			8:       string public _name;																							
			9:       string public _symbol;																							
																										
FALSE	The result of function calls should be cached rather than re-calling the function	The instances below point to the second+ call of the function within a single function																								
																										
																										
			/// @audit market.oracle() on line 918																							
			930:          (, info.oracleExchangeRate) = IOracle(market.oracle()).peek(																							
			/// @audit market.oracleData() on line 919																							
			931:              market.oracleData()																							
			/// @audit market.oracle() on line 918																							
			933:          info.spotExchangeRate = IOracle(market.oracle()).peekSpot(																							
			/// @audit market.oracleData() on line 919																							
			934:              market.oracleData()																							
																										
																										
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





																																																																					
																																																																																																																			
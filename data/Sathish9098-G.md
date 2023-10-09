TRUE	Use calldata instead of memory for read only arguments in external functions 																									

TRUE	State variables only set in the constructor should be declared immutable	Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).																								
																										
		While strings are not value types, and therefore cannot be immutable/constant if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract abstract with virtual functions for the string accessors, and having a child contract override the functions with the hard-coded implementation-specific values.																								
			/// @audit leverageModule (constructor)																							
			75:           leverageModule = USDOLeverageModule(_leverageModule);																							
			/// @audit marketModule (constructor)																							
			76:           marketModule = USDOMarketModule(_marketModule);																							
			/// @audit optionsModule (constructor)																							
			77:           optionsModule = USDOOptionsModule(_optionsModule);


TRUE	State variables can be packed into fewer storage slots	
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper																								
			/// @audit Variable ordering with 7 slots instead of the current 8:																							
			///           mapping(32):operators, uint256(32):totalFees, uint256(32):maxDebtRate, uint256(32):minDebtRate, uint256(32):debtRateAgainstEthMarket, uint256(32):debtStartPoint, user-defined(20):accrueInfo, bool(1):_isEthMarket																							
			46:       mapping(address => mapping(address => bool)) public operators;																							
																										
TRUE	Using storage instead of memory for structs/arrays saves gas	When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct																								
			File: tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol																							
			513:          IBigBang.AccrueInfo memory _accrueInfo = accrueInfo;																							
			525:          Rebase memory _totalBorrow = totalBorrow;																							
																										
																										
																										
																										
TRUE	State variables should be cached in stack variables rather than re-reading them from storage	The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.																								
																										
			/// @audit totalFees on line 446																							
			447:          feeShares = yieldBox.toShare(assetId, totalFees, false);																							
			/// @audit totalFees on line 447																							
			449:          if (totalFees > 0) {																							
			/// @audit totalFees on line 449																							
			450:              asset.approve(address(yieldBox), totalFees);	


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





																																																																					
																																																																																																																			
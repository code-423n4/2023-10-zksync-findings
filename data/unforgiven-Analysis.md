# comments for the judge
some of my reports are based on common patters and practices and standards. there are a lot of places that project implementation doesn't respect those patterns and standards are violated because of some trade-offs, but the point is what ever the reason, the issue exists there and just because there is a good reason behind issue doesn't mean the issue doesn't exists(for example the fact that "L2 transactions inclusion in batch and execution result can't be verified with published data on L1" is a bug, and it doesn't matter project decide this to have lower cost transactions).

I have a lot of issues that can be looked similar if one doesn't read them carefully, these issues have same keywords in their title, but they have different impacts, attack initiators, fix, access, and extra. I believe they should be treated as multiple issue. for example in case of L2SystemUpgrade transactions, I reported 5 cases that a upgrade transaction can totally block batch generation and upgrade mechanism in zkSync. the difference between them is that each of them is based on different parameter of upgrade transaction that validation function failed to validate them (`MaxFeePerGas`, `newProtocolVersion`, `maxPriorityFeePerGas` , ....) those issue shows multiple lacked checks in transaction validation function and each of them should treated separately.

as a last point, I need to mention that I don't have backstage access so if judge or sponsor have question about one of my issues please ask me directly.

# Approach taken in evaluating the codebase
first of all I didn't read and analysis the zkVM and circuits parts at all.
my approach was this:
1. first I read the whole documentation for 2 days fully. (there was some places that I didn't understand that I marked them to come back to them later)
2. then I read the codes while confirming the implementation with documentation. first I read l1 contracts code, then bootloader code, and in last L2 system contracts code. I didn't go depth in the code details in this step. but after this step which took 7 days I hade a overview of all the code base.
3. in this step I read documents again and tried to compare the code base with previous contests and I analyzed the changed parts for 2 days.
4. in this step I decided to read the code line by line and understands everything. most of the bugs I found was in this part.
5. in last 4 days I just write the reports. I ran the tests one time but I didn't tried to understand and play with the code with tests.

# Architecture recommendations
overall the architecture of zkSync was better than optimism network (I participated in optimism contest too). there was some shortcomings that were very strange, like:
- there was no respect to gas usage and gas perfectibility and gas efficiency in the docs or code base. user gas is just get used for everything, and most of the time there was gas left over(code didn't fully provide gas for transaction). this was very odd, because in all the bridge and multi chain projects I reviewed so far, gas was very important factor and handling correct gas for operations was important. for example in optimism half of the issues reported was because of the gas calculations. 
- there was no support for data availability for users, users and other off-chain system can't verify transactions easily.
- I would recommend to re design some part of L2 contracts that at least have some guaranteed minGas for operations.
- One architecture issue was that it won't be possible to bridge native L2 ERC20 tokens to Ethereum and zkSync was only working one way (`Ethereum native ERC20 -> zkSync`).
- ERC20 and WETH bridge didn't support sending "message + value" together and this will prevent contract to be able to work with them. it's better to add support for this.
- there were some access control logics(AllowList) that was mentioned they are temporary, there were a lot of bug in their implementation and design, I suggest to remove them so code become cleaner and those issue will be resolved.
- the Governance access for the L1 and L2 contract was unlimited and most of the time unchecked, Governance could have done anything. This is dangerous and allows for simple mistake to destroy everything. I suggest to implement better Governance system that Governance have access to predefined functionality. (for example Governance could delegate call to arbitrary address in most important contract in the project)
- There was a lot of back and force between bootloader and L2 SystemContracts, I think with a better middle layer library bootloader code can become shorter and simpler.

# Codebase quality analysis
Overall codebase quality was good, except the .yul files which had flat pattern implementation. I suggest if it is possible, separate bootloader logics between multiple files.
the problem with code base was there was written for different stage and levels and each part of the project is written with different language and different assumptions. it's very complicated to read some part of the codes like where code uses `call()` to perform special opcodes. I suggest writing a library or middle ware to hide this details and make code more clean. 
To have better code quality and simpler one, I suggest to sperate the codes that have different mixed partx (code have assembly and solidity together, or code have `call()` that simulates special opcodes and normal `call()` together)

# Centralization risks
There was a lot of centralization risks in the system, in fact nothing in the system was decentral. the 3 actor in the system was central entities. the risks are:
- Governance can steal all the funds in just on transactions.
- Governance can deploy any code in any address in L2.
- Admin can set no validator and the block generation will stop.
- Operator can censor transactions even the L1->L2 transaction are not enforced to be processed.

It is true that right now nothing was decentral but I think project is in right path and they are gradually upgrading the system, after reading docs and code multiple times the path they are taking are become clear for reader.

# Mechanism review
zkSync is Ethereum's L2 network, it may look very complicated but in short every L2 is consist of these things:
1. L1 contracts that receive and verify and store the L2 blocks info.
these contracts are: block receiver, multiple bridge contract, block verifier, governance 
2. an operator that collect transactions (L2 and L1->L2) and run them in node and generate the blocks.
operator mostly is trusted, projects are trying to decentralize the operator but currently it is central.
3. L2 contract that perform some logics in L2.
these contracts are: multiple bridge contract, some helper contracts to perform low level operation in VM
the point is that the project is very young and the outsider looking at project won't see any decentral part in it and it will be shocking, but project is upgrading different parts and walking through decentrality.

# Systemic risks
right now (that most components are central) most systematic risk for zkSync comes from Ethereum's gas price. validator need to publish batches in constant rate and if Ethereum gas price go higher then publishing batches will cause grief for validator.


# Systemic risks



### Time spent:
150 hours
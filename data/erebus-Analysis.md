# Analysis

(in the advanced category)

## Disclaimer

I do not know how many hours I have spent with your code for the past months (with `s`, as I started by chance a month before the contest was announced). Probably an average of $10$ to $12$ hours a day, each day of the week, is quite accurate. I have found **MANY** issues and I would have found more I had the chance to work 1:1 with one of you, but this is a public contest and I cannot monopolize you (thanks Vlad for your time and for hearing me out even at dawn). Anyway, this is gonna be a bit long and many of the things I am gonna say are not strictly related to the files in scope so judges may consider it like OOS or something like that. You know what? I do not give a fuck. Analysis must be made for the protocol as a whole, not doing ChatGPTish comments about the functions in each file in scope (I'm not gonna say names but some have been even selected as the best ones in past contests, which is quite concerning, as sponsors are paying for that shit).

What I'm trying to say is that they must provide value, **REAL VALUE**, for businesses, users and investors alike, so you are not gonna read here about how function $X$ does not check for `token` to not be non-zero or centralisation risk like *"owner can claim all user funds"*, which is bullshit in my opinion.

Enough with the rumbling, let's do business.

## High-level stuff

The codebase has some deficiencies in code documentation, as well as excesive complexity and potential integration risks with existant protocols, as new knowledge is needed on top of the usual *"dev stack"* which, let's be honest, if average developers are not even capable of securing their own projects in the *"Ethereum playground"*, the odds of screwing something up in zkSync Era are high. First things first, [code documentation](#code-documentation).

### Code documentation

I have talked with a few fella wardens and all of them say the same thing:

```
  Documentation sucks
```

Most comments in the codebase are either outdated, wrong or non-existant. I stopped writing typos after the 14th one (see my QA report, `NC-03`) and there are many more, some of them are as flagrant as puting a math equation as a reference which does not correlate with the implementation code (see [ecrecover/secp256k1/mod.rs, lines 56 to 58](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L56C1-L58C40)). There are even comments that relates to past ideas which are now outdated and won't be used (see [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/MsgValueSimulator.sol#L22) as well as the `verbatim.md` from the docs). 

Nonetheless, the worst is a clear sign of developer's laziness as **MANY** comments are copy-pasted *"as is"*, without taking into account the underlying logic of the commented code. Some of them are [in the deposit function of L1ERC20Bridge](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L162) (copied from [its counterpart in Mailbox](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L226)), in both [DefaultAccount](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L15) and [EmptyContract](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/EmptyContract.sol#L9), and [secp256k1/mod.rs, line 486](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L486) (copy-pasted from [secp256k1/mod.rs, line 401](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L401) without the corresponding code underneath, so the comment is useless), to name a few. 

Some files are verbose AF like [DiamondProxy](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol), even when there is no need as [the EIP 2535 is detailed enough](https://eips.ethereum.org/EIPS/eip-2535) and the code is fairly the same, whilst other parts of the codebase lack a clear explanation of what is going on (some ZK circuits).

From the provided documentation in the [docs folder](https://github.com/code-423n4/2023-10-zksync/tree/main/docs), the [EraVM Fromal specification](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/VM%20Section/EraVM%20Formal%20specification.pdf) feels a bit messy, the code formalisation is unreadable (it would be nice if it were written in some high-level language like Rust instead of that thing, as many developers out there may not understand what's written RN in the code formalisation) and some *"variable names"* in some equations do not correlate to their code counterpart (the fee model document, for example, has different variable names for the gas calculations in the document, the L1 contracts and the bootloader), which makes tracing back the functions and testing their correctness a bit difficult.

Moreover, most of your repos do have the same `README` or a very similar one (see [here](https://github.com/matter-labs/zksync-era), [here](https://github.com/matter-labs/era-contracts), [here](https://github.com/matter-labs/era-system-contracts), [here](https://github.com/matter-labs/era-sync_vm) and [here](https://github.com/matter-labs/era-boojum-cuda)), without explaining too much what the repo is about or how does it work. I found it pretty annoying when I started as I couldn't link them (what is currently deployed? is the zkEVM being used or the non-zk one? many branches show activity whilst the `main` one does not seem like it is being updated, which one is the correct one? ...). That harms enthusiast and independent developers from joining you and developing alongside you, which is one of the main pillars (if not the only one) of open source software. It is not transparency, it is open collaboration.

I do not want to make this analysis like *"ragging about the code"*. It's exactly the opposite. I like the project, I do know some of the sponsors and they are nice people and I have had a great time deep-diving in this codebase. If my words sound too harsh is because future developers and security researchers will need to understand your product before they start working on top of it, so having a poor or wrong documentation can lead to the current developers answering the same questions all the time, developing wrong products that may become hacked in the first month, affecting the reputation of zkSync Era, or, even worst, showing investors and future users an image of a team that does not really care about what they are doing. 

Remember:

    If something looks bad, it may be bad. 
    
You do **NOT** want your product to **EVER** look bad, which leads to our next section.

### UI/UX

Let's be honest. Mass adoption needs standards. Right now, your average Bob-user is not a tech person, he will be some random trader or retail investor that seeks to earn some profit with crypto. So he **NEEDS** some *"familiarity"* wherever he goes to increase the odds of investing their money. One of such places our little Bob will go is [block explorers](https://explorer.zksync.io/). Although the UI/UX feels like an Etherscan-derivated explorer, it is not. That differences makes our Bob be a little reserved when using it, as many features currently supported by Etherscan-like explorers are not supported by your block explorer (first negative point). If he goes to the [token list](https://explorer.zksync.io/tokenlist) he will see that **MANY** token prices are **WRONG** (Ethereum is listed at $1631 when it is, at the moment I am writing this, at $1676). Moreover, some of them like [rETH](https://explorer.zksync.io/address/0x32Fd44bB869620C0EF993754c8a00Be67C464806) are non-standard tokens but they are deployed as simple OpenZeppelin `ERC20Upgradeable` (with `L2standardERC20` as a wrapper around), so our little Bob may purchase any of them with the Ethereum version in mind just to discover that such a custom functionality is not there (rebasing, fee-on-transfer...). Second negative point.

From a developer POV, trying to standarize automated tools that make use of the Etherscan API in your chain would lead to tweaking their product to match your own API (I have not found it, so I guess it is not public yet). As I said, standards are key in this industry, so third negative point.

### Complexity

I did not deep-dived in the circuits nor in the VM implementation to make a full analysis of this part, I will talk about the feeling I had when reviewing the system contracts and the **HUGE** differences at the *"programmability"* level, from the new concept of frames and near calls to the fact that Ethereum transfers are first routed through `MsgValueSimulator` and `L2EthToken`. For example, such a difference has led to a bug in your implementation of the `EXTCODEHASH` opcode via the function `getCodeHash` in `AccountStorage` (see my medium submission `Calling getCodeHash for touched accounts will return 0 instead of EMPTY_STRING_KECCAK`). If you did just copied that part from Ethereum *"as is"*, then you would have not suffered from that.

Anyway, for a product like yours to be successful it needs a vibrant and **SECURE** ecosystem. Some people do not know this, but when a project in an experimental layer like zkSync Era gets rekt, the one who suffers the most (after users) is the layer in which the exploited project was deployed, not the project per se as it does not have an *"associated-dependant entity"* (following the decentralized nature of crypto, businesses behind such a project going bankrupt are just collateral damages, as corporate-managed projects are not decentralized by definition... not the place to discuss this, keep going). The reason behind is because there are **MANY** others out there trying to win market share and the most valuable active in crypto is not money, but trust, so even if another layer is a shit compared to zkSync Era, if your ecosystem gets rekt every two week users **AND THEIR MONEY** will migrate to that shitty layer just because it wasn't hacked before (we, as humans, are stupid, yes, keep going). Although many popular projects like Uniswap, Aave and the others are not deployed **YET** in zkSync Era (from your [ecosystem portal](https://zksync.dappradar.com/?category-dew=defi)), if they do and by chance they get hacked **ON** zkSync Era due to a inherent feature of the layer per se, that would be the worst from a layer POV, as the trust in your product would drop.

My recomendation in this part is to abstract as much as possible all the nuisances of your VM away from developers (via the compiler), so the same *"knowledge stack"* average Ethereum folks have can be applied without changes to zkSync Era. That way it is not your fault per se if they got exploited, as it could have been the same if the project was deployed on Ethereum instead.

## Contract specifics

There are a few parts of the protocol that do not look right. I know that it is a work in progress and **MANY** features are not even implemented but branding in the startup world is a **MUST** since day **0** (even more in crypto). Because of that there must **NOT** be contradictions in your words and your actions, as well as you must remove any idea of *"rug-pullers"* from the mind of everyone.

The first thing I wanna talk in this section is blockchain immutability. It is *"common knowledge"* that once you submit a transaction or deploy a contract, it will remain there *"forever"* (if hard-forking is not done and all of that). Because of that, users are willing to spend their money as, in theory, if I have $X$ tokens at my address $Y$ **NO ONE** will ever take them unless I let them (without taking into account bugs). If that weren't the case, would you think during the previous bull-run entities like BlackRock or Vanguard would put **BILLIONS** of dollars in circulation in crypto? Definetly not. You cannot scale without whales, and one of the requirements they do have to put such amounts of money in your product is immutability, which you do **NOT** have. I'm talking about force deploys, yes. Such a feature is dangerous AF, because if it was known by anyone, would you think they would bridge their funds to zkSync Era? Knowing that a malicious governor can just put a contract bytecode in your address that drains all your funds without you knowing anything (via shadow upgrades)? **NO** and you better remove that feature soon as it can be a game-killer for you. For the time being, just do this, for god's shake:

[**ContractDeployer, function forceDeployOnAddress**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L211C1-L233C6)

```diff
    /// @notice The method that can be used to forcefully deploy a contract.
    /// @param _deployment Information about the forced deployment.
    /// @param _sender The `msg.sender` inside the constructor call.
    function forceDeployOnAddress(ForceDeployment calldata _deployment, address _sender) external payable onlySelf {
+       require(uint256(uint160(_deployment.newAddress)) <= MAX_SYSTEM_CONTRACT_ADDRESS, "Do not do that");
        _ensureBytecodeIsKnown(_deployment.bytecodeHash);

        AccountInfo memory newAccountInfo;
        newAccountInfo.supportedAAVersion = AccountAbstractionVersion.None;
        // Accounts have sequential nonces by default.
        newAccountInfo.nonceOrdering = AccountNonceOrdering.Sequential;
        _storeAccountInfo(_deployment.newAddress, newAccountInfo);

        _constructContract(
            _sender,
            _deployment.newAddress,
            _deployment.bytecodeHash,
            _deployment.input,
            false,
            _deployment.callConstructor
        );

        emit ContractDeployed(_sender, _deployment.bytecodeHash, _deployment.newAddress);
    }
```

You even have your [ZK Credo](https://github.com/zksync/credo) that talks about freedom and all of that. Whilst this is true and a valid aspiration (I support that, even if my words do not reflect it) what I said above is a clear violation of that credo and breaking the trust users give you believing you will act accordingly.

There is another issue that triggers me and is the huge complexity of the fee model. I know you are a business and I know better than anyone that startups need profit, but when someone reads your [docs section about the fee model](https://era.zksync.io/docs/reference/concepts/fee-model.html) or even starts reading your codebase, it feels like you are trying to take as much pennies as possible (because that's what it is, without taking into account my submission `Operator can steal all gas provided by ANY user for L1 → L2 transactions`) from users. I know it is pretty complex to estimate the costs but, even if my words sound vague and ambiguous, try simplifying it and explaining it in your [docs section](https://era.zksync.io/docs/reference/concepts/fee-model.html) like users were stupid, possibly with an example like *"You wanna send 5 bytes to zkSync Era... the maths are like this... because of this and this..."*. Nothing more, nathing else. Just show users a simple example like when you google around seeing how would be taxed your income in different countries or whatever.

The last thing about your contracts/structural design I would like to say (I have more, but I do not want this to seem like the Bible) is related to the fees (again). You can argue that Ethereum is what it is today although their **EXTREME** fees and moving your costs up or down a few *"milidollars"* (it does not exist, but you know what I am trying to say) is not worth it. Well, we do spend most of our time with crypto-related stuff so we may not remember that, currently, users in the real world pay **0 FEES**. **0**. Nothing. You send $X$ dollars to your grandma and the world can be immersed in WWIII that you can be sure she will receive $X$ dollars. The fact that Ethereum is a collosus right now even with the high fees is because they were the *"first after Bitcoin"* (and they marketed it pretty well), so you shall not take for granted that users won't mind paying a bit more by using youur product. They **WON'T**. If Polygon or Solana do charge 10% of what you charge, they will go to Polygon or Solana even if they are not the *"new kid in the room"* (talking about ZK stuff). They even have a better ecosystem than you, as they have been *"alive"* more time, so the incentives users have to use zkSync Era aren't sustancial. It can be seen from the [inflows and metrics from defillama](https://defillama.com/chain/zkSync%20Era) and I can even guess that a part of the TVL that is RN in your system is from your own pockets from your last $200M fundraising round, so, although you are placed in the top 19 by TVL and 12 by stables, keep in mind that what brings users and money are **VALUABLE** ecosystem projects (not shitty memecoins), so you better partner with the leading projects out there, and simple **AND SECURE** development stack, so you better abstract all the complexity zkSync Era *"programming paradigm"* has (*"passing 0 gas to a near call gives all the gas of the upper frame..."*, *"new address derivation which may break "same addresses in all chains" projects"* and all of that).

## Summary

I could have written much more, but I did remember that the pot of this contest is a million and a few more, so there will be **LOTS** of submissions and I do not wanna make anyone spend more time reading this. I like the project? Yes. I wanna see it succeed? Yes. So, even if my words were too harsh, keep in mind what I said at the top of this analysis:

```
    If something looks bad, it may be bad
```
I do not want this for your product to be bad, I want it to be as perfect as possible. But that needs time, and time is something we do not have if a bull-run starts in a month. If you want to survive and succeed, you better prepare and polish your product, as the next wave of users will be non-tech ones and we all know what happens when they panic and governments start putting their noses and laws to *"secure"* crypto.


### Time spent:
500 hours
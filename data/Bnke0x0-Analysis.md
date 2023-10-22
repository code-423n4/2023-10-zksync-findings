zkSync, an Ethereum Layer 2 scaling solution utilizing ZK Rollups technology, inaugurated its mainnet, known as the Fair Onboarding Alpha, on February 16, 2023. During this initial stage, 
the network exclusively accommodated registered projects for testing, and access was not extended to end users. On March 24, 2023, zkSync Era, previously referred to as zkSync 2.0, was officially unveiled to the general public. 
Concurrently, zkSync 1.0 was rebranded as zkSync Lite.


### Codebase Analysis:
The codebase of zksync era is well-structured and follows best practices for smart contract development. It makes use of well-tested libraries and frameworks such as OpenZeppelin for implementing standard functionalities. The code is modular, which makes it easier to understand and maintain. It also has extensive comments, which aids in understanding the functionality of different parts of the code.
However, the complexity of the system also poses potential risks. With so many interacting components, there is a higher chance of unexpected behaviour or vulnerabilities. Therefore, thorough future testing and auditing are crucial to ensure the security of the system.


### Approach taken in evaluating the codebase:
While the codebase is comparatively easy to understand, comprehending the true business logic and intention is not. I needed to go through the specs numerous times and eventually approached OpenZeppelin & Spearbit's zkSync audit reports. 
That's where I started seeing some vulnerabilities that might not have been fully mitigated.

### Centralization risks: 
Currently, the governor wields the extraordinary authority to instantaneously enhance contracts and indefinitely suspend them. However, it's important to note that this authority is temporary. 
In the future, upgrades will be subjected to time restrictions, and the duration of suspensions will be constrained. These measures are designed to prevent any potential misuse of the governor's authority.


### Testing Coverage Recommendations:
The audit has identified several concerns regarding the testing of the current system. While specific findings have been highlighted for the contracts within the scope of this audit, it is imperative to consider the broader systematic risks.
Insufficient testing, although not indicative of a specific vulnerability, raises the likelihood of undiscovered vulnerabilities and bugs. Furthermore, it amplifies numerous interrelated risk factors within a complex code base featuring novel functionality. This deficiency includes the absence of a complete implicit specification of functionality and exact expected behaviors typically provided by tests, thus increasing the likelihood of missing correctness issues. It also necessitates more effort to establish fundamental correctness, diminishing the resources available for exploring edge cases and consequently heightening the possibility of overlooking intricate issues.
Moreover, the absence of routine automated testing for the full specification heightens the risk of introducing breaking changes and new vulnerabilities. This holds true for both previously audited code and future modifications to currently audited code. This concern is particularly pronounced in the project due to the rapid pace, extensive scope, and complexity of ongoing and planned changes across all aspects of the stack, including L1, L2, bootloader and system contracts, compiler, and VM. Ill-defined interfaces and assumptions contribute to the risk of subtle integration issues, which could be mitigated through comprehensive testing to enforce a thorough specification.
To address these concerns, we strongly recommend the implementation of a comprehensive, multi-level test suite prior to the next scheduled audits. This test suite should encompass contract-level tests achieving 95% to 100% coverage, deployment and integration tests specific to each layer, testing deployment scripts, as well as assessing the system as a whole. Additionally, layer-specific fork tests should be conducted for planned upgrades, and cross-chain full integration tests of the entire system should be performed.
Of utmost importance, the test suite must be well-documented to allow a reviewer to independently set up and run these test layers without direct reliance on the development team. We can provide examples of existing setups for reference in subsequent discussions. The implementation of such a comprehensive test suite should be given the highest priority to enhance the system's resilience and minimize the risk of vulnerabilities and bugs.




### Time spent:
14 hours


### Time spent:
14 hours
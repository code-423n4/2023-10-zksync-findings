Location:

Function Address.functionStaticCall(address,bytes,string) (code/system-contracts/contracts/openzeppelin/utils/Address.sol#196-209) contains a low level call to a custom address

Description:

No calls to custom addresses and contract interactions through access control

Recommendation:

Do not let calls to project contracts or do not give rights to a contract which can perform calls

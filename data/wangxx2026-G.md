## L2ERC20Bridge.l1TokenAddress can use _l1Token to save gas for the key

#### Save gas
   8625

#### Detail
The code is changed to the following way. It only needs to calculate the expectedL2Token when it is created for the first time. Subsequent users do not need to create it.
```solidity
        address expectedL2Token = l1TokenAddress[_l1Token];
        if (expectedL2Token == address(0)) {
            address expectedL2Token = l2TokenAddress(_l1Token);
            address deployedToken = _deployL2Token(_l1Token, _data);
            require(deployedToken == expectedL2Token, "mt");
            l1TokenAddress[_l1Token] = expectedL2Token;
        }

```   

#### Links
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L75-L76
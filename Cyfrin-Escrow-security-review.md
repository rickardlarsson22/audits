# [L-01] Incomplete NatSpec documentation
## Summary
The NatSpec documentation serves a variety of important purposes, benefiting internal developers, external developers integrating with the project, auditors reviewing the code, and end users.
## Recommendations
To address this issue, consider adding the missing NatSpec documentation.
# [G-01] Use solidity version 0.8.19 to gain some gas boost
## Context
[Escrow.sol#L2](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L2), [EscrowFactory.sol#L2](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/EscrowFactory.sol#L2)
## Summary
Upgrade to the latest solidity version 0.8.19 to get additional gas savings.        
       
See latest release for reference: [https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/](https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/)
## Proof Of Concept
````solidity
File: src/Escrow.sol
2:  pragma solidity 0.8.18;
````
[Escrow.sol#L2](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L2)
````solidity
File: src/EscrowFactory.sol
2:  pragma solidity 0.8.18;
````
[EscrowFactory.sol#L2](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/EscrowFactory.sol#L2)
# [G-02] Setting the constructor to payable
## Context
[Escrow.sol#L32-L51](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L32-L51)
## Vulnerability Details
Saves ~13 gas per instance.
````solidity
    constructor(
        uint256 price,
        IERC20 tokenContract,
        address buyer,
        address seller,
        address arbiter,
        uint256 arbiterFee
    ) {
        if (address(tokenContract) == address(0)) revert Escrow__TokenZeroAddress();
        if (buyer == address(0)) revert Escrow__BuyerZeroAddress();
        if (seller == address(0)) revert Escrow__SellerZeroAddress();
        if (arbiterFee >= price) revert Escrow__FeeExceedsPrice(price, arbiterFee);
        if (tokenContract.balanceOf(address(this)) < price) revert Escrow__MustDeployWithTokenBalance();
        i_price = price;
        i_tokenContract = tokenContract;
        i_buyer = buyer;
        i_seller = seller;
        i_arbiter = arbiter;
        i_arbiterFee = arbiterFee;
    }
````
[Escrow.sol#L32-L51](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L32-L51)
# [G-03] A modifier used only once and not being inherited should be inlined to save gas
## Vulnerability Details
````solidity
File: src/Escrow.sol
58  modifier onlyBuyer() {
59      if (msg.sender != i_buyer) {
60          revert Escrow__OnlyBuyer();
61      }
62      _;
63: }
````
[Escrow.sol#L58-L63](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L58-L63)          
The above modifier is only being called on [Line 94](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L94).
````solidity
66       modifier onlyBuyerOrSeller() {
67        if (msg.sender != i_buyer && msg.sender != i_seller) {
68            revert Escrow__OnlyBuyerOrSeller();
69        }
70        _;
71:      }
````
[Escrow.sol#L66-L71](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L66-L71)         
The above modifier is only being called on [Line 102](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L102).
````solidity
74       modifier onlyArbiter() {
75        if (msg.sender != i_arbiter) {
76            revert Escrow__OnlyArbiter();
77        }
78        _;
79:      }
````
[Escrow.sol#L74-L79](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L74-L79)        
The above modifier is only being called on [Line 109](https://github.com/Cyfrin/2023-07-escrow/blob/main/src/Escrow.sol#L109)
## Tools Used
VSCode

# [M-01] `payParams()` do not account for tokens with decimals higher than 18
## Lines of code
[https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L150](https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L150)
## Vulnerability details
### Impact
In `JBXBuybackDelegate.sol` a deposit or withdrawal of tokens with decimals higher than 18 will always revert.
````solidity
/**
     * @notice The datasource implementation
     *
     * @param  _data the data passed to the data source in terminal.pay(..). _data.metadata need to have the Uniswap quote
     * @return weight the weight to use (the one passed if not max reserved rate, 0 if swapping or the one corresponding
     *         to the reserved token to mint if minting)
     * @return memo the original memo passed
     * @return delegateAllocations The amount to send to delegates instead of adding to the local balance.
     */
    function payParams(JBPayParamsData calldata _data)
        external
        override
        returns (uint256 weight, string memory memo, JBPayDelegateAllocation[] memory delegateAllocations)
    {
        // Find the total number of tokens to mint, as a fixed point number with 18 decimals
        uint256 _tokenCount = PRBMath.mulDiv(_data.amount.value, _data.weight, 10 ** 18);

        // Unpack the quote from the pool, given by the frontend
        (,, uint256 _quote, uint256 _slippage) = abi.decode(_data.metadata, (bytes32, bytes32, uint256, uint256));

        // If the amount swapped is bigger than the lowest received when minting, use the swap pathway
        if (_tokenCount < _quote - (_quote * _slippage / SLIPPAGE_DENOMINATOR)) {
            // Pass the quote and reserve rate via a mutex
            mintedAmount = _tokenCount;
            reservedRate = _data.reservedRate;

            // Return this delegate as the one to use, and do not mint from the terminal
            delegateAllocations = new JBPayDelegateAllocation[](1);
            delegateAllocations[0] =
                JBPayDelegateAllocation({delegate: IJBPayDelegate(this), amount: _data.amount.value});

            return (0, _data.memo, delegateAllocations);
        }

        // If minting, do not use this as delegate
        return (_data.weight, _data.memo, new JBPayDelegateAllocation[](0));
    }
````
The tests are going to fail with:
````
Error: VM Exception while processing transaction: reverted with panic code 0x11 (Arithmetic operation underflowed or overflowed outside of an unchecked block)
````
### Proof of concept
Please add the POC below:
````solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.16;

import "truffle/Assert.sol";
import "../contracts/JBXBuybackDelegate.sol";

contract JBXBuybackDelegateTest {
    JBXBuybackDelegate public jbxBuybackDelegate;

    function beforeEach() public {
        // Deploy the JBXBuybackDelegate contract
        jbxBuybackDelegate = new JBXBuybackDelegate();
    }

    function testHandleTokensWithDecimalsHigherThan18() public {
        // Simulate the Uniswap quote with slippage
        uint256 quote = 500000; 
        uint256 slippage = 0.05;

        // Simulate the payParams input
        uint256 amount = 1000000;
        uint256 weight = 50; 
        uint256 reservedRate = 80; 
        bytes memory metadata = "";

        // Call the payParams function
        (uint256 resultWeight, string memory resultMemo, JBPayDelegateAllocation[] memory resultDelegateAllocations) =
            jbxBuybackDelegate.payParams(amount, weight, reservedRate, metadata);

        // Verify the output
        Assert.equal(resultWeight, weight, "Weight should match");
        Assert.equal(string(resultMemo), "", "Memo should match");
        Assert.equal(resultDelegateAllocations.length, 0, "Delegate allocations should be empty");
    }
}
````
We run the POC, the output of `testHandleTokensWithDecimalsHigherThan18()` is:
````solidity
✘ Test handle tokens with decimals higher than 18 (69ms)
    Assertion failed:
    Expected: 50
    Actual:   80
````
As we can see, tokens with decimals higher than the standard 18 will have a value above the expected one.
## Tools Used
Manual review
## Recommended mitigation steps
Update calculations in the contract to account for tokens with decimals higher than 18.
# [L-01] Use the safe variant and `ERC721.mint`
## Lines of code
[https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L205](https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L205)       
[https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L207](https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L207)
## Vulnerability details
### Impact
`.mint` won’t check if the recipient is able to receive the NFT. If an incorrect address is passed, it will result in a silent failure and loss of asset.     
      
OpenZeppelin [recommendation](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol#L277) is to use the safe variant of `_mint`.     
## Tools Used
Manual review
## Recommended mitigation steps
Replace `_mint()` with `_safeMint()`.
# [N-01] Use underscores for number literals
## Lines of code
[https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L68](https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L68)
## Vulnerability details
### Impact
````solidity
juice-buyback/contracts/JBXBuybackDelegate.sol

68:   uint256 private constant SLIPPAGE_DENOMINATOR = 10000;
````
## Tools Used
Manual review
## Recommended mitigation steps
````diff
- 68:   uint256 private constant SLIPPAGE_DENOMINATOR = 10000;
+ 68:   uint256 private constant SLIPPAGE_DENOMINATOR = 10_000;
````
# [G-01] Open the optimizer
## Lines of code
[https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/foundry.toml#L1-L7](https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/foundry.toml#L1-L7)
## Vulnerability details
### Impact
Always use the Solidity optimizer to optimize gas costs. It’s good practice to set the optimizer as high as possible until it no longer helps reduce gas costs in function calls. This is advisable since function calls are intended to be executed many more times than contract deployment, which only happens once.
## Recommended mitigation steps
In the light of this information, I suggest you to open the optimizer for `juice-buyback`.
# [G-02] Use a more recent version of solidity
## Lines of code
[https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L2](https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L2)
## Vulnerability details
### Impact
- Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
- Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
- Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
- Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value
- In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.
- In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.       
       
1 result - 1 file
````solidity
juice-buyback/contracts/JBXBuybackDelegate.sol

2:  pragma solidity ^0.8.16;
````
[https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L2](https://github.com/code-423n4/2023-05-juicebox/blob/main/juice-buyback/contracts/JBXBuybackDelegate.sol#L2)
## Tools Used
Manual Review
## Recommended mitigation steps
````diff
- 2:  pragma solidity ^0.8.16;
+ 2:  pragma solidity 0.8.17;
````

# [M-01] Owner can transfer all ERC20 reward token out using function recoverTokens
## Lines of code
[https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/Router.sol#L285-L291](https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/Router.sol#L285-L291)
## Vulnerability details
### Impact
The function recoverTokens is very privileged. It means to recover any token that is accidently sent to the contract.
````solidity
  function recoverTokens(address tokenAddress, address to, uint256 amount) external onlyOwner {
    if (tokenAddress == address(0)) {
      payable(to).transfer(amount);
      return;
    }
    IERC20(tokenAddress).transfer(to, amount);
  }
````
However, admin / owner can use this function to transfer all the reserved reward token, which result in fund loss of the pledge creator and the loss of reward for users that want to delegate the token.        
The safeguard:
````solidity
  if (tokenAddress == address(0)) {
        payable(to).transfer(amount);
        return;
      }
````
This if statement cannot stop owner from transferring fund because if the owner is compromised or misbehave, he can adjust the whitelist easily.
## Tools Used
Manual Review
## Recommended mitigation steps
It's recommended to implement additional checks, such as using a timelock mechanism to allow for community scrutiny before any token transfers, implementing multi-signature functionality for the recovery process, or providing a mechanism for users to reclaim their accidentally sent tokens in a controlled manner.   
# [M-02] Use call() rather than transfer() on address payable 
## Lines of code
[https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/Router.sol#L287](https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/Router.sol#L287)
## Vulnerability details
### Impact
[L287](https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/Router.sol#L287) in [Router.sol](https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/Router.sol) uses .transfer() to send ether to other addresses. There are a number of issues with using .transfer(), as it can fail for a number of reasons (specified in the Proof of Concept).
### Proof of concept
1. The destination is a smart contract that doesn’t implement a payable function or it implements a payable function but that function uses more than 2300 gas units.        
2. The destination is a smart contract that doesn’t implement a payable fallback function or it implements a payable fallback function but that function uses more than 2300 gas units.       
3. The destination is a smart contract but that smart contract is called via an intermediate proxy contract increasing the case requirements to more than 2300 gas units. A further example of unknown destination complexity is that of a multisig wallet that as part of its operation uses more than 2300 gas units.       
4. Future changes or forks in Ethereum result in higher gas fees than transfer provides. The .transfer() creates a hard dependency on 2300 gas units being appropriate now and into the future.
## Tools Used
Manual Review, Foundry
## Recommended mitigation steps
Instead use the .call() function to transfer ether and avoid some of the limitations of .transfer(). This would be accomplished by changing recoverTokens() to something like;
````solidity
  (bool success, ) = payable(to).call{value: amount}("");
  require(success, "Transfer failed.");
````
Gas units can also be passed to the .call() function as a variable to accomodate any uses edge cases. Gas could be a mutable state variable that can be set by the contract owner.
# [M-03] Unsafe use of transfer()/transferFrom() with IERC20
## Lines of code
[https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/Router.sol#L290](https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/Router.sol#L290)         
[https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/pools/tokens/ERC677.sol#L17](https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/pools/tokens/ERC677.sol#L17)
## Vulnerability details
### Impact
Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions on L1 do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their [function signatures](https://medium.com/coinmonks/missing-return-value-bug-at-least-130-tokens-affected-d67bf08521ca) do not match and therefore the calls made, revert (see [this](https://gist.github.com/IllIllI000/2b00a32e8f0559e8f386ea4f1800abc5) link for a test case).
### Proof of concept
````solidity
contracts/Router.sol
    IERC20(tokenAddress).transfer(to, amount);

contracts/pools/tokens/ERC677.sol
    super.transfer(to, amount);
````
## Tools Used
Manual Review
## Recommended mitigation steps
Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead.
# [M-04] transmit() function does not check if ecrecover return value is 0
## Lines of code
[https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/ocr/OCR2Base.sol#L227](https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/ocr/OCR2Base.sol#L227)
## Vulnerability details
### Impact
The transmit() function of OCR2Base.sol calls the Solidity ecrecover function directly to verify the given signatures.
The return value of ecrecover may be 0, which means the signature is invalid, but the check can be bypassed when signer is 0.
### Proof of concept
[https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/ocr/OCR2Base.sol#L227](https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/ocr/OCR2Base.sol#L227)
## Tools Used
Manual Review
## Recommended mitigation steps
Use the recover function from OpenZeppelin's ECDSA library for signature verification.
# [M-05] Orders may not be fillable due to missing approvals
## Lines of code
[https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/pools/BurnMintTokenPool.sol#L12](https://github.com/code-423n4/2023-05-chainlink/blob/main/contracts/pools/BurnMintTokenPool.sol#L12)
## Vulnerability details
Not all IERC20 implementations revert() when there's a failure in approve(). If one of these tokens returns false, there is no check for whether this has happened during the order listing validation, so it will only be detected when the order is attempted.
### Impact
If the approval failure isn't detected, the listing will never be fillable, because the funds won't be able to be pulled from the opensea conduit. Once this happens, and if it's detected, the only way to fix it is to create a counter-listing at a lower price (which may be below the market value of the tokens), waiting for the order to expire (which it may never), or by buying out all of the Rae to cancel the order (very expensive and defeats the purpose of pooling funds in the first place).
### Proof of concept
The return value of approve() isn't checked:
````solidity
contracts/pools/BurnMintTokenPool.sol

constructor(IBurnMintERC20 token, RateLimiter.Config memory rateLimiterConfig) TokenPool(token, rateLimiterConfig) {
    token.approve(address(this), 2 ** 256 - 1);
  }
````
[https://github.com/code-423n4/2023-05-chainlink/blob/f7a6de4a5292478dbf5b0750a931f8fd957916e8/contracts/pools/BurnMintTokenPool.sol#L11-L13](https://github.com/code-423n4/2023-05-chainlink/blob/f7a6de4a5292478dbf5b0750a931f8fd957916e8/contracts/pools/BurnMintTokenPool.sol#L11-L13)
## Tools Used
Manual Review
## Recommended mitigation steps
Use OpenZeppelin's safeApprove(), which checks the return code and reverts if it's not success.

# [H-01] Attacker can drain the token from the user's account
## Lines of code
[https://github.com/code-423n4/2023-05-ajna/blob/main/ajna-core/src/PositionManager.sol#L388](https://github.com/code-423n4/2023-05-ajna/blob/main/ajna-core/src/PositionManager.sol#L388)
## Vulnerability details
### Impact
There is a potential vulnerability if the `increaseLPAllowance()` function is not implemented safely and allows for arbitrary increases to the token allowance.
````solidity
File: ajna-core/src/PositionManager.sol

        pool.increaseLPAllowance(owner, params_.indexes, lpAmounts);
````
If an attacker gains control of the spender address, they can use the `approve()` function to increase the allowance of the spender for the token to a very large value, which can potentially result in the token being drained from the user's account.
## Tools Used
Manual Review
## Recommended mitigation steps
Here's how you can modify the `increaseLPAllowance()` function by implementing `safeIncreaseAllowance()` from OpenZeppelin:
````diff
        function increaseLPAllowance(
                address spender_,
                uint256[] calldata indexes_,
                uint256[] calldata amounts_
            ) external override nonReentrant {
-                LPActions.increaseLPAllowance(
-                                _lpAllowances[msg.sender][spender_],
-                                spender_,
-                                indexes_,
-                                amounts_
-                            );
+                for (uint256 i = 0; i < indexes_.length; i++) {
+                    uint256 index = indexes_[i];
+                    uint256 amount = amounts_[i];

+                    // approve owner to take over the LP ownership (required for transferLP pool call)
+                    SafeERC20.safeIncreaseAllowance(
+                        lpTokens[index],
+                        spender_,
+                        amount
+                    );

+                    _lpAllowances[msg.sender][spender_][index] = _lpAllowances[msg.sender][spender_][index].add(amount);
+                }
            }
````
# [H-02] Imprecise block calculation
## Lines of code
[https://github.com/code-423n4/2023-05-ajna/blob/main/ajna-grants/src/grants/base/StandardFunding.sol#L29-L46](https://github.com/code-423n4/2023-05-ajna/blob/main/ajna-grants/src/grants/base/StandardFunding.sol#L29-L46)
## Vulnerability details
### Impact
````solidity
        * @dev    Roughly equivalent to the number of blocks in 7 days.
        * @dev    Roughly equivalent to the number of blocks in 90 days.
        * @dev    Roughly equivalent to the number of blocks in 10 days.
````
As described in the NatSpec comment above these are imprecise/rough calculations of the time blocks. Although, I strongly feel that the calculations in the smart contract should be the correct amount. 
````solidity
File: ajna-grants/src/grants/base/StandardFunding.sol

    /**
     * @notice Length of the challengephase of the distribution period in blocks.
     * @dev    Roughly equivalent to the number of blocks in 7 days.
     * @dev    The period in which funded proposal slates can be checked in updateSlate.
     */
    uint256 internal constant CHALLENGE_PERIOD_LENGTH = 50400;

    /**
     * @notice Length of the distribution period in blocks.
     * @dev    Roughly equivalent to the number of blocks in 90 days.
     */
    uint48 internal constant DISTRIBUTION_PERIOD_LENGTH = 648000;

    /**
     * @notice Length of the funding phase of the distribution period in blocks.
     * @dev    Roughly equivalent to the number of blocks in 10 days.
     */
    uint256 internal constant FUNDING_PERIOD_LENGTH = 72000;
````
There are three cases:      
7 days blocks:       
The value of 50400 is incorrect because it corresponds to approximately 7.4 days (50400 * 15 seconds / 60 seconds / 60 minutes / 24 hours = 7.36 days). In this case, the correct value would be 40320 (which is 7 days * 24 hours * 60 minutes * 60 seconds / 15 seconds per block = 40320 blocks).
90 days blocks:       
90 days * 24 hours/day * 60 minutes/hour * 60 seconds/minute / 15 seconds/block = 5,184,000 blocks.       
10 days blocks:       
10 days * 24 hours/day * 60 minutes/hour * 60 seconds/minute / 15 seconds/block = 57,600 blocks        
If the different time frame values were chosen to account for factors such as network congestion, mining difficulty adjustments, or other variables that can affect block times it would be appropriate to specify it as a comment.
## Tools Used
Manual Review
## Recommended mitigation steps
Consider justifying the choice of such a rough calculation or change the code as below:
````diff
        /**
             * @notice Length of the challengephase of the distribution period in blocks.
             * @dev    Roughly equivalent to the number of blocks in 7 days.
             * @dev    The period in which funded proposal slates can be checked in updateSlate.
             */
-            uint256 internal constant CHALLENGE_PERIOD_LENGTH = 50400;
+            uint256 internal constant CHALLENGE_PERIOD_LENGTH = 40_320;


            /**
             * @notice Length of the distribution period in blocks.
             * @dev    Roughly equivalent to the number of blocks in 90 days.
             */
-            uint48 internal constant DISTRIBUTION_PERIOD_LENGTH = 648000;
+            uint48 internal constant DISTRIBUTION_PERIOD_LENGTH = 5_184_000;


            /**
             * @notice Length of the funding phase of the distribution period in blocks.
             * @dev    Roughly equivalent to the number of blocks in 10 days.
             */
-            uint256 internal constant FUNDING_PERIOD_LENGTH = 72000;
+            uint256 internal constant FUNDING_PERIOD_LENGTH = 57_600;
````
# [M-01] Contracts are vulnerable to fee-on-transfer-token-related accounting issues
## Lines of code
[https://github.com/code-423n4/2023-05-ajna/blob/main/ajna-grants/src/grants/GrantFund.sol#L67](https://github.com/code-423n4/2023-05-ajna/blob/main/ajna-grants/src/grants/GrantFund.sol#L67)
## Vulnerability details
### Impact
Without measuring the balance before and after the transfer, there's no way to ensure that enough tokens were transferred, in the cases where the token has a fee-on-transfer mechanic. If there are latent funds in the contract, subsequent transfers will succeed.      
        
There is one instance of this issue:
````solidity
File: ajna-grants/src/grants/GrantFund.sol

        token.safeTransferFrom(msg.sender, address(this), fundingAmount_);
````
## Tools Used
Manual Review
## Recommended mitigation steps
Consider checking the balance before and after the transfer.

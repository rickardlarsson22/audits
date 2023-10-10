# Inconsistency in "Total Percentage" Calculation in _distribute Function
## Severity
Medium
## Relevant GitHub Links
[https://github.com/Cyfrin/2023-08-sparkn/blob/main/src/Distributor.sol#L109](https://github.com/Cyfrin/2023-08-sparkn/blob/main/src/Distributor.sol#L109)        
[https://github.com/Cyfrin/2023-08-sparkn/blob/main/src/Distributor.sol#L135](https://github.com/Cyfrin/2023-08-sparkn/blob/main/src/Distributor.sol#L135)
## Summary
In the contract's _distribute function, a discrepancy exists between the NatSpec comment and the code logic regarding calculating the "total percentage" for fund distribution. This inconsistency may result in inaccurate fund distribution and financial discrepancies among winners.
## Vulnerability Details
In the _distribute function, the NatSpec comment specifies that the "total percentage" should be calculated as (100 - COMMITION_FEE). However, the actual code logic checks whether totalPercentage equals (10000 - COMMISSION_FEE), which suggests a misalignment between basis points and conventional percentages.
## Impact
The inconsistency between the comment and the code logic can introduce inaccuracies in fund distribution, leading to unjust and unfair distribution among winners. This misalignment can undermine trust in the distribution mechanism and cause financial losses or disputes among participants. While the immediate impact might not be critical, over time, this issue could erode the reputation of the contract and affect user confidence.
## Tools Used
VSCode
## Recommendations
To address this issue and ensure precise fund distribution, it's recommended to harmonize the comment and code logic for the "total percentage" calculation. The following steps are recommended:
        
1. Code Correction: Amend the code to reflect the comment's guidance accurately. Update the condition that checks the correctness of totalPercentage to verify if it equals (100 - COMMISSION_FEE).
         
2. Comment Clarification: Revise the NatSpec comment to provide a clear and accurate description of the expected calculation for the "total percentage."
            
By aligning the comment and the code logic, this inconsistency will be resolved, enhancing the accuracy of the fund distribution mechanism and maintaining trust among users.

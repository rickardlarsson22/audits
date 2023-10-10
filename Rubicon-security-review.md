# Detailed Findings
# [M-01] Position accidentally inherits `Ownable` instead of `Owner` which removes an important safeguard without sponsor knowledge
## Lines of code
[Position.sol#L15](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/utilities/poolsUtility/Position.sol#L15), [PoolsUtility.sol#L62](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/utilities/poolsUtility/PoolsUtility.sol#L62)
## Vulnerability details
### Impact
Position may accidentally transfer ownership to inoperable address due to perceived safeguard that doesn't exist
### Proof of concept
````solidity
contracts/utilities/poolsUtility/Position.sol

15:   contract Position is Ownable, DSMath {
````
[Position.sol#L15](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/utilities/poolsUtility/Position.sol#L15)       
Position inherits from `Ownable` rather than `Owner`, which is the intended contract.
Owner overwrites the critical Ownable#transferOwnership function to make the ownership transfer process a two step process.
This adds important safeguards because in the event that the target is unable to accept for any reason (input typo, incompatible multisig/contract, etc.) the ownership transfer process will fail because the pending owner will not be able to accept the transfer.
````solidity
contracts/utilities/poolsUtility/PoolsUtility.sol

62:   position.transferOwnership(msg.sender);
````
[PoolsUtility.sol#L62](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/utilities/poolsUtility/PoolsUtility.sol#L62)       
To make matters worse, since it only overwrites the `transferOwnership()` function present in the PoolsUtility contract, the Position contract will function as intended just without this safeguard. It is likely that the owner won't even realize until too late and the safeguard has failed.
A perceived safeguard where there isn't one is more damaging than not having any safeguard at all.
## Tools Used
Manual Review
## Recommended mitigation steps
````diff
-  15:   contract Position is Ownable, DSMath {
+  15:   contract Position is Owner, DSMath {
````

# [M-02] As protocol relies heavily on admin actions, single-step ownership transfer pattern is dangerous
## Lines of code
[Position.sol#L15](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/utilities/poolsUtility/Position.sol#L15)
## Vulnerability details
### Description
````solidity
contracts/utilities/poolsUtility/Position.sol

15:   contract Position is Ownable, DSMath {
````
Inheriting from OpenZeppelin's `Ownable` contract means you are using a single-step ownership transfer pattern. If an admin provides an incorrect address for the new owner this will result in none of the `onlyOwner` marked methods being callable again. The better way to do this is to use a two-step ownership transfer approach, where the new owner should first claim its new rights before they are transferred.
## Tools Used
Manual Review
## Recommended mitigation steps
Use OpenZeppelin's `Ownable2Step` instead of `Ownable`

# [L-01] Oracle failure check
## Lines of code
[Position.sol#L316](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/utilities/poolsUtility/Position.sol#L316)
## Vulnerability details
### Proof of concept
````solidity
contracts/utilities/poolsUtility/Position.sol

316:    uint256 _price = oracle.getUnderlyingPrice(CToken(_bathToken));
````
## Tools Used
Manual Review
## Recommended mitigation steps
It is good to check the returned value from oracle is sane (i.e. if it is zero) and prepare for a fallback.

# [L-02] Call to `Position.openPosition()` can reenter
## Lines of code
[Position.sol#L125-L206](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/utilities/poolsUtility/Position.sol#L125-L206)
## Vulnerability details
### Impact
The openPosition function in Position does several external calls to open a position. This call is unsafe, as the recipient can be malicious and do a reentrancy call.
## Tools Used
Manual Review
## Recommended mitigation steps
Consider adding a `nonReentrant` modifier to the method.

# [L-03] Use `2StepSetOwner` instead of `setOwner`
## Lines of code
[RubiconMarket.sol#L25-L28](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/RubiconMarket.sol#L25-L28)
## Vulnerability details
### Proof of concept
````solidity
contracts/RubiconMarket.sol

25      function setOwner(address owner_) external auth {
26          owner = owner_;
27          emit LogSetOwner(owner);
28:     }
````
## Tools Used
Manual Review
## Recommended mitigation steps
Use a 2 structure which is safer.

# [I-01] Using `SafeMath` when compiler is `^0.8.0`
## Lines of code
[BathBuddy.sol#L2](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/periphery/BathBuddy.sol#L2), [BathBuddy.sol#L6](https://github.com/code-423n4/2023-04-rubicon/blob/main/contracts/periphery/BathBuddy.sol#L6)
## Vulnerability details
There is no need to use `SafeMath` when compiler is `^0.8.0` because it has built-in under/overflow checks.

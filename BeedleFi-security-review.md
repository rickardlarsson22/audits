# [L-01] Floating pragma
## Vulnerability Details
In the three smart contracts in scope for this audit, the floating pragma ^0.8.18 is employed. This practice of locking the pragma version helps to enforce that contracts are not unintentionally deployed using a different pragma.       
By doing so, the contract system avoids potential issues that may arise from utilizing outdated pragma versions that could introduce bugs or from using recently released pragma versions that might have unknown security vulnerabilities.
````solidity
src\interfaces\IERC20.sol
  2: pragma solidity ^0.8.19;

src\interfaces\ISwapRouter.sol
  2: pragma solidity ^0.8.19;

src\utils\Errors.sol
  2: pragma solidity ^0.8.19;

src\utils\Ownable.sol
  2: pragma solidity ^0.8.19;

src\utils\Structs.sol
  2: pragma solidity ^0.8.19;

src\Beedle.sol
  2: pragma solidity ^0.8.19;

src\Fees.sol
  2: pragma solidity ^0.8.19;

src\Lender.sol
  2: pragma solidity ^0.8.19;

src\Staking.sol
  2: pragma solidity ^0.8.19;
````
## Recommendations
It is recommended to lock the pragma version with a known bug-free compiler version. Avoid using floating pragma in the final live deployment whenever possible. By specifying a fixed compiler version, you ensure that the bytecode produced remains consistent across different builds. This is particularly crucial if you rely on bytecode-level verification of the code.
````diff
-      pragma solidity ^0.8.19;
+      pragma solidity 0.8.19;
````
# [L-02] Lack of a double-step transferOwnership patter
## Vulnerability Details
The current ownership transfer process for all the contracts inheriting from Ownable or OwnableUpgradeable involves the current owner calling the [transferOwnership()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v4.8/contracts/access/Ownable.sol#L69-L72) function:
````solidity
19:  function transferOwnership(address _owner) public virtual onlyOwner {//@audit no 2step transfer
20:          owner = _owner;
21:         emit OwnershipTransferred(msg.sender, _owner);
22:      }
````
[Ownable.sol#L19-L22](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L19-L22)        
In case the nominated EOA account is invalid, there is a significant possibility that the owner might unintentionally transfer ownership to an uncontrolled account. This can result in the loss of access to all functions associated with the `onlyOwner` modifier, presenting a potential risk.
## Tools Used
VSCode
## Recommendations
To mitigate this risk, it is advisable to adopt a two-step process. In this process, the owner nominates an account, and the nominated account must then execute an `acceptOwnership()` function to complete the ownership transfer successfully. This approach ensures that the nominated EOA account is both valid and active. To implement this, you can utilize OpenZeppelin's [Ownable2Step contract](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v4.8/contracts/access/Ownable2Step.sol) as a replacement for `Ownable`.
````solidity
52:  function acceptOwnership() external {
53:      address sender = _msgSender();
54:      require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
55:      _transferOwnership(sender);
56:  }
````
[Ownable2Step.sol#L52-L56](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v4.8/contracts/access/Ownable2Step.sol#L52-L56)
# [I-01] Missing address(0) checks in Ownable.sol
## Vulnerability Details
The constructor() function lacks zero address checks on owner addresses, which could potentially lead to unintended consequences. If, by mistake, the zero address is set as a valid owner address, it could wrongly validate any invalid signature as a whitelisted address.
````solidity
14:    constructor(address _owner) {
15:        owner = _owner;
16:        emit OwnershipTransferred(address(0), _owner);
17:    }
````
[Ownable.sol#L14-L17](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L14-L17)
## Tools Used
VSCode
## Recommendations
It is crucial to include zero address checks for owner addresses to prevent the aforementioned issues.
````diff
    constructor(address _owner) {
+       require(_owner != address(0), "address(0)")
        owner = _owner;
        emit OwnershipTransferred(address(0), _owner);
    }
````
# [G-01] Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate
## Vulnerability Details
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.       
Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.
## Proof of Concept
````solidity
File: src/Staking.sol
18  /// @notice mapping of user indexes
19  mapping(address => uint256) public supplyIndex;
20    
21  /// @notice mapping of user balances
22  mapping(address => uint256) public balances;
23  /// @notice mapping of user claimable rewards
24: mapping(address => uint256) public claimable;
````
[Staking.sol#L18-L24](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L18-L24)
## Tools Used
VSCode
## Recommendations
Consider editing to the following struct:
````diff
-  mapping(address => uint256) public supplyIndex;
-  mapping(address => uint256) public balances;
-  mapping(address => uint256) public claimable;
  
+  struct addressBalancesStruct {
+    uint256 supplyIndex;
+    uint256 balances;
+    uint256 claimable;
+  }
````

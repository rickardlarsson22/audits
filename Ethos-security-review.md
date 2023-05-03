# Introduction

A time-boxed security contest review of the **Ethos Reserve** protocol was done by **rickardlarsson22**, with a focus on the security and gas aspects of the application's implementation.

# Disclaimer

A smart contract security review can never verify the complete absence of vulnerabilities. This is a time, resource and expertise bound effort where I try to find as many vulnerabilities as possible. I can not guarantee 100% security after the review or even if the review will find any problems with your smart contracts.

# About **rickardlarsson22**

Rickard Larsson, or **rickardlarsson22**, is an independent smart contract security researcher. Having found numerous security vulnerabilities in various protocols, he does his best to contribute to the blockchain ecosystem and its protocols by putting time and effort into security research & reviews.      
Reach out on Twitter [@rickardlarsson6](https://twitter.com/rickardlarsson6).

### Scope

The following smart contracts were in scope of the audit:

- `Ethos-Core/contracts/CollateralConfig.sol`
- `Ethos-Core/contracts/BorrowerOperations.sol`
- `Ethos-Core/contracts/TroveManager.sol`
- `Ethos-Core/contracts/ActivePool.sol`
- `Ethos-Core/contracts/StabilityPool.sol`
- `Ethos-Core/contracts/LQTY/CommunityIssuance.sol`
- `Ethos-Core/contracts/LQTY/LQTYStaking.sol`
- `Ethos-Core/contracts/LUSDToken.sol`
- `Ethos-Vault/contracts/ReaperVaultV2.sol`
- `Ethos-Vault/contracts/ReaperVaultERC4626.sol`
- `Ethos-Vault/contracts/abstract/ReaperBaseStrategyV4.sol`
- `Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol`
	
The following number of issues were found, categorized by their severity:

- High: 1 issue
- Medium: 2 issues
- Low: 9 issues
- Non-critical: 23 issues
- Informational: 1 issue
- Refactor: 1 issue
- Ordinary: 2 issues
- Suggestions: 2 issues
- Gas Optimizations: 16 issues

---

# Findings Summary

| ID     | Title                                                                                                                       | Severity          |
| ------ | --------------------------------------------------------------------------------------------------------------------------- | ----------------- |
| [H-01] | `ReaperVaultV2.sol` is not EIP-4626 compliant                                                                               | High              |
| [M-01] | Unhandled return values of `transfer`                                                                                       | Medium            |
| [M-02] | No storage gap for upgradeable contracts                                                                                    | Medium            |
| [L-01] | Owner can renounce Ownership                                                                                                | Low               |
| [L-02] | Missing `event` for critical parameters init and change                                                                     | Low               |
| [L-03] | Missing ReEntrancy Guard to `withdraw` function                                                                             | Low               |
| [L-04] | Lack of ReEntrancy Guards                                                                                                   | Low               |
| [L-05] | `initialize()` function can be called by anybody                                                                            | Low               | 
| [L-06] | Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from `uint256`                  | Low               |
| [L-07] | Front running attacks by the `onlyOwner`                  								       | Low               |
| [L-08] | A single point of failure                  								       		       | Low               |
| [L-09] | Minting tokens to the zero address should be avoided                  						       | Low               |
| [N-01] | For modern and more readable code; update import usages                                                                     | Non-Critical      |
| [N-02] | Add a timelock to critical functions                                                                                        | Non-Critical      |
| [N-03] | Constants should be defined rather than using magic numbers                                                                 | Non-Critical      |
| [N-04] | Interchangeable usage of `uint` and `uint256`                                                                               | Non-Critical      |
| [N-05] | Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)                                            | Non-Critical      |
| [N-06] | Use of `bytes.concat()` instead of `abi.encodePacked()`                                                                     | Non-Critical      |
| [N-07] | Signature Malleability of EVM’s `ecrecover()`                                                                               | Non-Critical      |
| [N-08] | `2**<n> - 1` should be re-written as `type(uint<n>).max`                                                                    | Non-Critical      |
| [N-09] | Not using the latest version of OpenZeppelin from dependencies                                                              | Non-Critical      |
| [N-10] | `constant` redefined elsewhere                                                                                              | Non-Critical      |
| [N-11] | Showing the actual values of numbers in NatSpec comments makes checking and reading code easier                             | Non-Critical      |
| [N-12] | Inconsistent Solidity Versions                                                                                              | Non-Critical      |
| [N-13] | Inconsistent spacing in contract                                                                                            | Non-Critical      |
| [N-14] | Lack of event emission after critical `initialize()` function                                                               | Non-Critical      |
| [N-15] | Mark visibility of `initialize()` functions as `external`                                                                   | Non-Critical      |
| [N-16] | Constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`                              | Non-Critical      |
| [N-17] | Assembly Codes Specific – Should Have Comments                                                                              | Non-Critical      |
| [N-18] | Use `require` instead of `assert`                                                                                           | Non-Critical      |
| [N-19] | NatSpec comments should be increased in contracts                                                 			       | Non-Critical      |
| [N-20] | `Function writing` that does not comply with the `Solidity Style Guide`                                          	       | Non-Critical      |
| [N-21] | Include return parameters in NatSpec comments                                          	       			       | Non-Critical      |
| [N-22] | Long lines are not suitable for the `Solidity Style Guide`                                          	       		       | Non-Critical      |
| [N-23] | For functions, follow Solidity standard naming conventions (internal function style rule)                                   | Non-Critical      |
| [I-01] | `event` is not emitted when an important action happens on-chain                                                            | Informational     |
| [R-01] | Some number values can be refactored with `_`                                                                               | Refactor          |
| [O-01] | Floating `pragma`                                                                               			       | Ordinary     	   |
| [O-02] | Use a more recent version of Solidity                                                                                       | Ordinary          |
| [S-01] | Project Upgrade and Stop Scenario should be                                                                                 | Suggestions       |
| [S-02] | Use descriptive names for Contracts and Libraries                                                                           | Suggestions       |
| [G-01] | `bytes constant` are more efficient than `string constant`                                                                  | Gas Optimizations |
| [G-02] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a `struct`, where appropriate        | Gas Optimizations |
| [G-03] | `require()` or `revert()` statements that check input arguments should be at the top of the function                        | Gas Optimizations |
| [G-04] | Using `unchecked` blocks to save gas - Increments in `for` loop can be unchecked ( save 30-40 gas per loop iteration)       | Gas Optimizations |
| [G-05] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | Gas Optimizations |
| [G-06] | `internal` functions only called once can be inlined to save gas                                                            | Gas Optimizations |
| [G-07] | Structs can be packed into fewer storage slots                                                                              | Gas Optimizations |
| [G-08] | Using `storage` instead of `memory` for structs/arrays saves gas                                                            | Gas Optimizations |
| [G-09] | Splitting `require()` statements that use `&&` saves gas                                                                    | Gas Optimizations |
| [G-10] | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead                                                    | Gas Optimizations |
| [G-11] | `public` functions not called by the contract should be declared `external` instead                                         | Gas Optimizations |
| [G-12] | Using Solidity version `0.8.17` will provide an overall gas optimization                                                    | Gas Optimizations |
| [G-13] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables                                                      | Gas Optimizations |
| [G-14] | `keccak256()` should only need to be called on a specific string literal once                                               | Gas Optimizations |
| [G-15] | Remove the `initializer` modifier                                                                                           | Gas Optimizations |
| [G-16] | Use constants instead of `type(uintx).max`                                                                                  | Gas Optimizations |
| [G-17] | Use nested `if` and, avoid multiple check combinations                                                                      | Gas Optimizations |
| [G-18] | Setting the constructor to `payable`                                                                      		       | Gas Optimizations |
| [G-19] | Use `assembly` to write address storage values                                                                      	       | Gas Optimizations |
| [G-20] | Use a more recent version of solidity                                                                      	       	       | Gas Optimizations |

# Detailed Findings

# [H-01] `ReaperVaultV2.sol` is not EIP-4626 compliant

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L356](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L356)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L426](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L426)

## Vulnerability details
### Impact
Other protocols that integrate with the Ethos Reserve may mistakenly use the following code with no trace of a `mint()` and `redeem()` function.     
Judging this as High Risk. EIP4626 is aimed to create a consistent and robust implementation patterns for Tokenized Vaults. A slight deviation from 4626 would break composability and potentially lead to loss of funds.       
       
The missing functions are the most problematic; one expects the `mint()` and `redeem()` to be present, but they’re absent instead.      
It might cause integration problems in the future that can lead to wide range of issues for both parties.
### Proof of Concept
All official EIP-4626 requirements can be found on it's [official page](https://eips.ethereum.org/EIPS/eip-4626#methods).    
The following functions are missing but should be present:   
1. `mint(uint256, address) returns (uint256)`
2. `redeem(uint256, address, address) returns (uint256)`   
### Tools Used
VS Code
### Recommended Mitigation Steps
Consider implementing the two functions listed above to meet the specifications of EIP-4626.

# [M-01] Unhandled return values of `transfer`

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L198](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L198)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L203](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L203)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L222](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L222)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L237](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L237)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L127](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L127)   
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L135](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L135)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L171](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L171)
## Vulnerability details
### Impact
ERC20 implementations are not always consistent. Some implementations of `transfer` could return ‘false’ on failure instead of reverting. It is safer to wrap such calls into `require()` statements to these failures.
### Proof of Concept
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L198](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L198)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L203](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L203)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L222](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L222)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L237](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L237)
````solidity
  _transfer(_sender, _poolAddress, _amount);

  _transfer(_poolAddress, _receiver, _amount);

  _transfer(msg.sender, recipient, amount);

  _transfer(sender, recipient, amount);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L127](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L127)
````solidity
  OathToken.transfer(_account, _OathAmount);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L135](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L135)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L171](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L171)
````solidity
  lusdToken.transfer(msg.sender, LUSDGain);

  lusdToken.transfer(msg.sender, LUSDGain);
````
### Tools Used
VS Code
### Recommended Mitigation Steps
Check the return value and revert on 0/false or use OpenZeppelin’s SafeERC20 wrapper functions.
# [M-02] No storage gap for upgradeable contracts

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L14-L19](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L14-L19)
## Vulnerability details
### Impact
For SafeAccessControlEnumerableUpgradeable and SafeOwnableUpgradeable, which are upgradeable abstract contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable abstract contract without causing storage collisions, a storage gap should be added to the upgradeable abstract contract.   

If no storage gap is added, when the upgradable abstract contract introduces new variables, it may override the variables in the inheriting contract.
### Proof of Concept
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L14-L19](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L14-L19)
````solidity
   abstract contract ReaperBaseStrategyv4 is
      ReaperAccessControl,
      IStrategy,
      UUPSUpgradeable,
      AccessControlEnumerableUpgradeable
  {
````
### Tools Used
VS Code
### Recommended Mitigation Steps
Consider adding a storage gap at the end of the upgradeable abstract contract:   
````solidity
  uint256[50] private __gap;
````















# [L-01] Owner can renounce Ownership

## Vulnerability details
### Proof of Concept
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L14-L19](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L14-L19)
````solidity
7 results - 7 files
/ActivePool.sol
13: import "./Dependencies/Ownable.sol";
26: contract ActivePool is Ownable, CheckContract, IActivePool {

/BorrowerOperations.sol
13: import "./Dependencies/Ownable.sol";
18: contract BorrowerOperations is LiquityBase, Ownable, CheckContract, IBorrowerOperations {

/CollateralConfig.sol
6:  import "./Dependencies/Ownable.sol";
15: contract CollateralConfig is ICollateralConfig, CheckContract, Ownable {

/HintHelpers.sol
9:  import "./Dependencies/Ownable.sol";
12: contract HintHelpers is LiquityBase, Ownable, CheckContract {

/StabilityPool.sol
16: import "./Dependencies/Ownable.sol";
146:  contract StabilityPool is LiquityBase, Ownable, CheckContract, IStabilityPool {

/CommunityIssuance.sol
9:  import "./Dependencies/Ownable.sol";
14: contract CommunityIssuance is ICommunityIssuance, Ownable, CheckContract, BaseMath { 

/LQTYStaking.sol
7:  import "./Dependencies/Ownable.sol";
18: contract LQTYStaking is ILQTYStaking, Ownable, CheckContract, BaseMath {
````
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.     

The OpenZeppelin’s Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design.     

Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.     

`onlyOwner` functions:
````solidity
/ActivePool.sol
71: function setAddresses(
          address _collateralConfigAddress,
          address _borrowerOperationsAddress,
          address _troveManagerAddress,
          address _stabilityPoolAddress,
          address _defaultPoolAddress,
          address _collSurplusPoolAddress,
          address _treasuryAddress,
          address _lqtyStakingAddress,
          address[] calldata _erc4626vaults
      )
          external
          onlyOwner
      {
125:  function setYieldingPercentage(address _collateral, uint256 _bps) external onlyOwner {

132:  function setYieldingPercentageDrift(uint256 _driftBps) external onlyOwner {

138:  function setYieldClaimThreshold(address _collateral, uint256 _threshold) external onlyOwner {

144:  function setYieldDistributionParams(uint256 _treasurySplit, uint256 _SPSplit, uint256 _stakingSplit) external onlyOwner {

214:  function manualRebalance(address _collateral, uint256 _simulatedAmountLeavingPool) external onlyOwner {

/BorrowerOperations.sol
110:  function setAddresses(
        address _collateralConfigAddress,
        address _troveManagerAddress,
        address _activePoolAddress,
        address _defaultPoolAddress,
        address _stabilityPoolAddress,
        address _gasPoolAddress,
        address _collSurplusPoolAddress,
        address _priceFeedAddress,
        address _sortedTrovesAddress,
        address _lusdTokenAddress,
        address _lqtyStakingAddress
    )
        external
        override
        onlyOwner
    {

/CollateralConfig.sol
46: function initialize(
        address[] calldata _collaterals,
        uint256[] calldata _MCRs,
        uint256[] calldata _CCRs
    ) external override onlyOwner {

85: function updateCollateralRatios(
        address _collateral,
        uint256 _MCR,
        uint256 _CCR
    ) external onlyOwner checkCollateral(_collateral) {

/HintHelpers.sol
27: function setAddresses(
        address _collateralConfigAddress,
        address _sortedTrovesAddress,
        address _troveManagerAddress
    )
        external
        onlyOwner
    {

/StabilityPool.sol
261:  function setAddresses(
        address _borrowerOperationsAddress,
        address _collateralConfigAddress,
        address _troveManagerAddress,
        address _activePoolAddress,
        address _lusdTokenAddress,
        address _sortedTrovesAddress,
        address _priceFeedAddress,
        address _communityIssuanceAddress
    )
        external
        override
        onlyOwner
    {

/CommunityIssuance.sol
61: function setAddresses
    (
        address _oathTokenAddress, 
        address _stabilityPoolAddress
    ) 
        external 
        onlyOwner 
        override 
    {

101:  function fund(uint amount) external onlyOwner {

120:  function updateDistributionPeriod(uint256 _newDistributionPeriod) external onlyOwner {

/LQTYStaking.sol
66: function setAddresses
    (
        address _lqtyTokenAddress,
        address _lusdTokenAddress,
        address _troveManagerAddress, 
        address _borrowerOperationsAddress,
        address _activePoolAddress,
        address _collateralConfigAddress
    ) 
        external 
        onlyOwner 
        override 
    {
````

### Recommended Mitigation Steps
We recommend either reimplementing the function to disable it or to clearly specify if it is part of the contract design.
# [L-02] Missing `event` for critical parameters init and change

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L111-L125](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L111-L125)
## Vulnerability details
### Proof of Concept
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L111-L125](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L111-L125)
````solidity
    constructor(
        address _token,
        string memory _name,
        string memory _symbol,
        uint256 _tvlCap,
        address _treasury,
        address[] memory _strategists,
        address[] memory _multisigRoles
    ) ERC20(string(_name), string(_symbol)) {
        token = IERC20Metadata(_token);
        constructionTime = block.timestamp;
        lastReport = block.timestamp;
        tvlCap = _tvlCap;
        treasury = _treasury;
        lockedProfitDegradation = (DEGRADATION_COEFFICIENT * 46) / 10**6; // 6 hours in blocks
````
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

### Recommended Mitigation Steps
Add Event-Emit.

# [L-03] Missing ReEntrancy Guard to `withdraw` function

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L95-L103](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L95-L103)
## Vulnerability details
### Proof of Concept
ReaperBaseStrategyv4.sol contract has no Re-Entrancy protection in withdraw function.    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L95-L103](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L95-L103)
````solidity
    function withdraw(uint256 _amount) external override returns (uint256 loss) {
        require(msg.sender == vault, "Only vault can withdraw");
        require(_amount != 0, "Amount cannot be zero");
        require(_amount <= balanceOf(), "Ammount must be less than balance");


        uint256 amountFreed = 0;
        (amountFreed, loss) = _liquidatePosition(_amount);
        IERC20Upgradeable(want).safeTransfer(vault, amountFreed);
    }
````
If the mint was initiated by a contract without reentrancy guard, it will allow an attacker controlled contract to call the mint again, which may not be desirable to some parties, like allowing minting more than allowed.     
[https://www.paradigm.xyz/2021/08/the-dangers-of-surprising-code](https://www.paradigm.xyz/2021/08/the-dangers-of-surprising-code)

### Recommended Mitigation Steps
Use Openzeppelin or Solmate Re-Entrancy pattern.    
Here is a example of a re-entrancy guard:
````solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
contract ReEntrancyGuard {
    bool internal locked;
    modifier noReentrant() {
        require(!locked, "No re-entrancy");
        locked = true;
        _;
        locked = false;
    }
}
````

# [L-04] Lack of ReEntrancy Guards

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L142-L173](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L142-L173)
## Vulnerability details
### Proof of Concept
Whenever `lqtyToken.safeTransfer(msg.sender, LQTYToWithdraw);` is called, the `msg.sender` address can re-enter back to the contracts.    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L142-L173](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L142-L173)
````solidity
    function unstake(uint _LQTYamount) external override {
        uint currentStake = stakes[msg.sender];
        _requireUserHasStake(currentStake);


        // Grab any accumulated ETH and LUSD gains from the current stake
        (address[] memory collGainAssets, uint[] memory collGainAmounts) = _getPendingCollateralGain(msg.sender);
        uint LUSDGain = _getPendingLUSDGain(msg.sender);
        
        _updateUserSnapshots(msg.sender);


        if (_LQTYamount > 0) {
            uint LQTYToWithdraw = LiquityMath._min(_LQTYamount, currentStake);


            uint newStake = currentStake.sub(LQTYToWithdraw);


            // Decrease user's stake and total LQTY staked
            stakes[msg.sender] = newStake;
            totalLQTYStaked = totalLQTYStaked.sub(LQTYToWithdraw);
            emit TotalLQTYStakedUpdated(totalLQTYStaked);


            // Transfer unstaked LQTY to user
            lqtyToken.safeTransfer(msg.sender, LQTYToWithdraw);


            emit StakeChanged(msg.sender, newStake);
        }


        emit StakingGainsWithdrawn(msg.sender, LUSDGain, collGainAssets, collGainAmounts);


        // Send accumulated LUSD and ETH gains to the caller
        lusdToken.transfer(msg.sender, LUSDGain);
        _sendCollGainToUser(collGainAssets, collGainAmounts);
    }
````

### Recommended Mitigation Steps
Apply necessary reentrancy prevention by utilizing the OpenZeppelin’s nonReentrant modifier to block possible re-entrancy.

# [L-05] `initialize()` function can be called by anybody

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73)
## Vulnerability details
### Proof of Concept
`initialize()` function can be called anybody when the contract is not initialized.    
Also, there is no 0 address check in the address arguments of the `initialize()` function, which must be defined.

Here is a definition of `initialize()` function.     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73)
````solidity
62:     function initialize(
63:         address _vault,
64:         address[] memory _strategists,
65:         address[] memory _multisigRoles,
66:         IAToken _gWant
67:     ) public initializer {
68:         gWant = _gWant;
69:         want = _gWant.UNDERLYING_ASSET_ADDRESS();
70:         __ReaperBaseStrategy_init(_vault, want, _strategists, _multisigRoles);
71:        rewardClaimingTokens = [address(_gWant)];
72:     }
````

### Recommended Mitigation Steps
Add a control that makes `initialize()` only call the Deployer Contract:
````solidity
if (msg.sender != DEPLOYER_ADDRESS) {
						revert NotDeployer();
				}
````
Alternatively, add the `onlyOwner` modifier like it has been done in other contract's `initialize()` function.

# [L-06] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from `uint256`

## Vulnerability details
In the protocol, there are function's values with the risk of being downcasted.

### Recommended Mitigation Steps
Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from `uint256`.

# [L-07] Front running attacks by the `onlyOwner`

## Vulnerability details
`setAddresses` function can be triggered by onlyOwner and operations can be blocked.
````solidity
	function setAddresses
	    (
		address _lqtyTokenAddress,
		address _lusdTokenAddress,
		address _troveManagerAddress, 
		address _borrowerOperationsAddress,
		address _activePoolAddress,
		address _collateralConfigAddress
	    ) 
		external 
		onlyOwner
		override 
	    {
		checkContract(_lqtyTokenAddress);
		checkContract(_lusdTokenAddress);
		checkContract(_troveManagerAddress);
		checkContract(_borrowerOperationsAddress);
		checkContract(_activePoolAddress);
		checkContract(_collateralConfigAddress);

		lqtyToken = IERC20(_lqtyTokenAddress);
		lusdToken = ILUSDToken(_lusdTokenAddress);
		troveManagerAddress = _troveManagerAddress;
		borrowerOperationsAddress = _borrowerOperationsAddress;
		activePoolAddress = _activePoolAddress;
		collateralConfig = ICollateralConfig(_collateralConfigAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L71-L103](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L71-L103)
````solidity
    function setAddresses(
        address _collateralConfigAddress,
        address _troveManagerAddress,
        address _activePoolAddress,
        address _defaultPoolAddress,
        address _stabilityPoolAddress,
        address _gasPoolAddress,
        address _collSurplusPoolAddress,
        address _priceFeedAddress,
        address _sortedTrovesAddress,
        address _lusdTokenAddress,
        address _lqtyStakingAddress
    )
        external
        override
        onlyOwner
    {
        // This makes impossible to open a trove with zero withdrawn LUSD
        assert(MIN_NET_DEBT > 0);


        checkContract(_collateralConfigAddress);
        checkContract(_troveManagerAddress);
        checkContract(_activePoolAddress);
        checkContract(_defaultPoolAddress);
        checkContract(_stabilityPoolAddress);
        checkContract(_gasPoolAddress);
        checkContract(_collSurplusPoolAddress);
        checkContract(_priceFeedAddress);
        checkContract(_sortedTrovesAddress);
        checkContract(_lusdTokenAddress);
        checkContract(_lqtyStakingAddress);


        collateralConfig = ICollateralConfig(_collateralConfigAddress);
        troveManager = ITroveManager(_troveManagerAddress);
        activePool = IActivePool(_activePoolAddress);
        defaultPool = IDefaultPool(_defaultPoolAddress);
        stabilityPoolAddress = _stabilityPoolAddress;
        gasPoolAddress = _gasPoolAddress;
        collSurplusPool = ICollSurplusPool(_collSurplusPoolAddress);
        priceFeed = IPriceFeed(_priceFeedAddress);
        sortedTroves = ISortedTroves(_sortedTrovesAddress);
        lusdToken = ILUSDToken(_lusdTokenAddress);
        lqtyStakingAddress = _lqtyStakingAddress;
        lqtyStaking = ILQTYStaking(_lqtyStakingAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L110-L153](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L110-L153)
````solidity
    function setAddresses(
        address _collateralConfigAddress,
        address _sortedTrovesAddress,
        address _troveManagerAddress
    )
        external
        onlyOwner
    {
        checkContract(_collateralConfigAddress);
        checkContract(_sortedTrovesAddress);
        checkContract(_troveManagerAddress);


        collateralConfig = ICollateralConfig(_collateralConfigAddress);
        sortedTroves = ISortedTroves(_sortedTrovesAddress);
        troveManager = ITroveManager(_troveManagerAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L27-L41](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L27-L41)
````solidity
    function setAddresses(
        address _borrowerOperationsAddress,
        address _collateralConfigAddress,
        address _troveManagerAddress,
        address _activePoolAddress,
        address _lusdTokenAddress,
        address _sortedTrovesAddress,
        address _priceFeedAddress,
        address _communityIssuanceAddress
    )
        external
        override
        onlyOwner
    {
        checkContract(_borrowerOperationsAddress);
        checkContract(_collateralConfigAddress);
        checkContract(_troveManagerAddress);
        checkContract(_activePoolAddress);
        checkContract(_lusdTokenAddress);
        checkContract(_sortedTrovesAddress);
        checkContract(_priceFeedAddress);
        checkContract(_communityIssuanceAddress);


        borrowerOperations = IBorrowerOperations(_borrowerOperationsAddress);
        collateralConfig = ICollateralConfig(_collateralConfigAddress);
        troveManager = ITroveManager(_troveManagerAddress);
        activePool = IActivePool(_activePoolAddress);
        lusdToken = ILUSDToken(_lusdTokenAddress);
        sortedTroves = ISortedTroves(_sortedTrovesAddress);
        priceFeed = IPriceFeed(_priceFeedAddress);
        communityIssuance = ICommunityIssuance(_communityIssuanceAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L261-L291](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L261-L291)
````solidity
    function setAddresses
    (
        address _oathTokenAddress, 
        address _stabilityPoolAddress
    ) 
        external 
        onlyOwner 
        override 
    {
        require(!initialized, "issuance has been initialized");
        checkContract(_oathTokenAddress);
        checkContract(_stabilityPoolAddress);


        OathToken = IERC20(_oathTokenAddress);
        stabilityPoolAddress = _stabilityPoolAddress;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L61-L75](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L61-L75)
````solidity
    function setAddresses
    (
        address _lqtyTokenAddress,
        address _lusdTokenAddress,
        address _troveManagerAddress, 
        address _borrowerOperationsAddress,
        address _activePoolAddress,
        address _collateralConfigAddress
    ) 
        external 
        onlyOwner 
        override 
    {
        checkContract(_lqtyTokenAddress);
        checkContract(_lusdTokenAddress);
        checkContract(_troveManagerAddress);
        checkContract(_borrowerOperationsAddress);
        checkContract(_activePoolAddress);
        checkContract(_collateralConfigAddress);


        lqtyToken = IERC20(_lqtyTokenAddress);
        lusdToken = ILUSDToken(_lusdTokenAddress);
        troveManagerAddress = _troveManagerAddress;
        borrowerOperationsAddress = _borrowerOperationsAddress;
        activePoolAddress = _activePoolAddress;
        collateralConfig = ICollateralConfig(_collateralConfigAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L66-L91](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L66-L91)

### Recommended Mitigation Steps
Use a timelock to avoid instant changes of the parameters.

# [L-08] A single point of failure 

## Vulnerability details 
The `onlyOwner` role has a single point of failure and `onlyOwner` can use critical a few functions.       
      
Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.      
      
`onlyOwner` functions;
````solidity
14 results - 7 files

Ethos-Core/contracts/ActivePool.sol
71:	function setAddresses(
125:	function setYieldingPercentage(address _collateral, uint256 _bps) external onlyOwner {
132:	function setYieldingPercentageDrift(uint256 _driftBps) external onlyOwner {
138:	function setYieldClaimThreshold(address _collateral, uint256 _threshold) external onlyOwner {
144:	function setYieldDistributionParams(uint256 _treasurySplit, uint256 _SPSplit, uint256 _stakingSplit) external onlyOwner {
214:	function manualRebalance(address _collateral, uint256 _simulatedAmountLeavingPool) external onlyOwner {

Ethos-Core/contracts/BorrowerOperations.sol
110:	function setAddresses(

Ethos-Core/contracts/CollateralConfig.sol
46:	function initialize(
85:	function updateCollateralRatios(

Ethos-Core/contracts/HintHelpers.sol
27:	function setAddresses(

Ethos-Core/contracts/StabilityPool.sol
261:	function setAddresses(

Ethos-Core/contracts/LQTY/CommunityIssuance.sol
61:	function setAddresses
101:	function fund(uint amount) external onlyOwner {

Ethos-Core/contracts/LQTY/LQTYStaking.sol
66:	function setAddresses
````
This increases the risk of `A single point of failure`.

Recommended Mitigation Steps

### Recommended Mitigation Steps
Add a time lock to critical functions. Admin-only functions that change critical parameters should emit events and have timelocks.     
     
Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.      
      
Also detail them in documentation and NatSpec comments.

# [L-09] Minting tokens to the zero address should be avoided

## Vulnerability details 
`address(0)` check is missing in both these contracts, consider applying a check in the function to ensure tokens aren’t minted to the zero address.
````solidity
Ethos-Core/contracts/LUSDToken.sol
185	function mint(address _account, uint256 _amount) external override { 
186		_requireMintingNotPaused();
187		_requireCallerIsBorrowerOperations();
188		_mint(_account, _amount);
189:	    }

Ethos-Vault/contracts/ReaperVaultERC4626.sol
154	function mint(uint256 shares, address receiver) external override returns (uint256 assets) {
155		assets = previewMint(shares); // previewMint rounds up so exactly "shares" should be minted and not 1 wei less
156		_deposit(assets, receiver);
157:	    }
````
This increases the risk of `A single point of failure`.

Recommended Mitigation Steps

### Recommended Mitigation Steps
Add a time lock to critical functions. Admin-only functions that change critical parameters should emit events and have timelocks.     
     
Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.      
      
Also detail them in documentation and NatSpec comments.

# [N-01] For modern and more readable code; update import usages

## Vulnerability details
````solidity
109 results - 12 files

/ActivePool.sol
import './Interfaces/IActivePool.sol';// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ICollateralConfig.sol";// @audit n : For modern and more readable code, update import usages
import './Interfaces/IDefaultPool.sol';// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ICollSurplusPool.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ILQTYStaking.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/IStabilityPool.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ITroveManager.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/SafeMath.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/Ownable.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/CheckContract.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/console.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/SafeERC20.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/IERC4626.sol";// @audit n : For modern and more readable code, update import usages

/BorrowerOperations.sol
import "./Interfaces/IBorrowerOperations.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ICollateralConfig.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ITroveManager.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ILUSDToken.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ICollSurplusPool.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ISortedTroves.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ILQTYStaking.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/LiquityBase.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/Ownable.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/CheckContract.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/console.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/SafeERC20.sol";// @audit n : For modern and more readable code, update import usages

/CollateralConfig.sol
import "./Dependencies/CheckContract.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/Ownable.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/SafeERC20.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ICollateralConfig.sol";// @audit n : For modern and more readable code, update import usages

/LUSDToken.sol
import "./Interfaces/ILUSDToken.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ITroveManager.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/SafeMath.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/CheckContract.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/console.sol";// @audit n : For modern and more readable code, update import usages

/StabilityPool.sol
import './Interfaces/IBorrowerOperations.sol';// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ICollateralConfig.sol";// @audit n : For modern and more readable code, update import usages
import './Interfaces/IStabilityPool.sol';// @audit n : For modern and more readable code, update import usages
import './Interfaces/IBorrowerOperations.sol';// @audit n : For modern and more readable code, update import usages
import './Interfaces/ITroveManager.sol';// @audit n : For modern and more readable code, update import usages
import './Interfaces/ILUSDToken.sol';// @audit n : For modern and more readable code, update import usages
import './Interfaces/ISortedTroves.sol';// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ICommunityIssuance.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/LiquityBase.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/SafeMath.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/LiquitySafeMath128.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/Ownable.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/CheckContract.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/console.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/SafeERC20.sol";// @audit n : For modern and more readable code, update import usages

/TroveManager.sol
import "./Interfaces/ICollateralConfig.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ITroveManager.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/IStabilityPool.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ICollSurplusPool.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ILUSDToken.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ISortedTroves.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/ILQTYStaking.sol";// @audit n : For modern and more readable code, update import usages
import "./Interfaces/IRedemptionHelper.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/LiquityBase.sol";// @audit n : For modern and more readable code, update import usages
// import "./Dependencies/Ownable.sol";
import "./Dependencies/CheckContract.sol";// @audit n : For modern and more readable code, update import usages
import "./Dependencies/IERC20.sol";// @audit n : For modern and more readable code, update import usages

/CommunityIssuance.sol
import "../Dependencies/IERC20.sol"; // @audit n : For modern and more readable code, update import usages
import "../Interfaces/ICommunityIssuance.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/BaseMath.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/LiquityMath.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/Ownable.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/CheckContract.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/SafeMath.sol";// @audit n : For modern and more readable code, update import usages

/LQTYStaking.sol
import "../Dependencies/BaseMath.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/SafeMath.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/Ownable.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/CheckContract.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/console.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/IERC20.sol";// @audit n : For modern and more readable code, update import usages
import "../Interfaces/ICollateralConfig.sol";// @audit n : For modern and more readable code, update import usages
import "../Interfaces/ILQTYStaking.sol";// @audit n : For modern and more readable code, update import usages
import "../Interfaces/ITroveManager.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/LiquityMath.sol";// @audit n : For modern and more readable code, update import usages
import "../Interfaces/ILUSDToken.sol";// @audit n : For modern and more readable code, update import usages
import "../Dependencies/SafeERC20.sol";// @audit n : For modern and more readable code, update import usages

/ReaperStrategyGranarySupplyOnly.sol
import "./abstract/ReaperBaseStrategyv4.sol";// @audit n : For modern and more readable code, update import usages
import "./interfaces/IAToken.sol";// @audit n : For modern and more readable code, update import usages
import "./interfaces/IAaveProtocolDataProvider.sol";// @audit n : For modern and more readable code, update import usages
import "./interfaces/ILendingPool.sol";// @audit n : For modern and more readable code, update import usages
import "./interfaces/ILendingPoolAddressesProvider.sol";// @audit n : For modern and more readable code, update import usages
import "./interfaces/IRewardsController.sol";// @audit n : For modern and more readable code, update import usages
import "./libraries/ReaperMathUtils.sol";// @audit n : For modern and more readable code, update import usages
import "./mixins/VeloSolidMixin.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts-upgradeable/utils/math/MathUpgradeable.sol";// @audit n : For modern and more readable code, update import usages

/ReaperVaultERC4626.sol
import "./ReaperVaultV2.sol";// @audit n : For modern and more readable code, update import usages
import "./interfaces/IERC4626Functions.sol";// @audit n : For modern and more readable code, update import usages

/ReaperVaultV2.sol
import "./interfaces/IERC4626Events.sol";// @audit n : For modern and more readable code, update import usages
import "./interfaces/IStrategy.sol";// @audit n : For modern and more readable code, update import usages
import "./libraries/ReaperMathUtils.sol";// @audit n : For modern and more readable code, update import usages
import "./mixins/ReaperAccessControl.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts/access/AccessControlEnumerable.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts/utils/math/Math.sol";// @audit n : For modern and more readable code, update import usages

/ReaperBaseStrategyv4.sol
import "../interfaces/IStrategy.sol"; // @audit n : For modern and more readable code, update import usages
import "../interfaces/IVault.sol";// @audit n : For modern and more readable code, update import usages
import "../libraries/ReaperMathUtils.sol";// @audit n : For modern and more readable code, update import usages
import "../mixins/ReaperAccessControl.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts-upgradeable/access/AccessControlEnumerableUpgradeable.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";// @audit n : For modern and more readable code, update import usages
import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";// @audit n : For modern and more readable code, update import usages

````

### Recommended Mitigation Steps
`import {contract1 , contract2} from "filename.sol";`
````solidity
import {BaseMath} from "../Dependencies/BaseMath.sol";
````

# [N-02] Add a timelock to critical functions

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L71-L103](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L71-L103)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L110-L153](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L110-L153)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L261-L291](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L261-L291)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L232-L278](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L232-L278)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L61-L75](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L61-L75)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L66-L91](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L66-L91)
## Vulnerability details
It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).    

Consider adding a timelock to:
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L71-L103](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L71-L103)
````solidity
    function setAddresses(
...        
        collateralConfigAddress = _collateralConfigAddress;
        borrowerOperationsAddress = _borrowerOperationsAddress;
        troveManagerAddress = _troveManagerAddress;
        stabilityPoolAddress = _stabilityPoolAddress;
        defaultPoolAddress = _defaultPoolAddress;
        collSurplusPoolAddress = _collSurplusPoolAddress;
        treasuryAddress = _treasuryAddress;
        lqtyStakingAddress = _lqtyStakingAddress;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L110-L153](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L110-L153)
````solidity
    function setAddresses(
...    
        collateralConfig = ICollateralConfig(_collateralConfigAddress);
        troveManager = ITroveManager(_troveManagerAddress);
        activePool = IActivePool(_activePoolAddress);
        defaultPool = IDefaultPool(_defaultPoolAddress);
        stabilityPoolAddress = _stabilityPoolAddress;
        gasPoolAddress = _gasPoolAddress;
        collSurplusPool = ICollSurplusPool(_collSurplusPoolAddress);
        priceFeed = IPriceFeed(_priceFeedAddress);
        sortedTroves = ISortedTroves(_sortedTrovesAddress);
        lusdToken = ILUSDToken(_lusdTokenAddress);
        lqtyStakingAddress = _lqtyStakingAddress;
        lqtyStaking = ILQTYStaking(_lqtyStakingAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L261-L291](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L261-L291)
````solidity
    function setAddresses(
...     
        borrowerOperations = IBorrowerOperations(_borrowerOperationsAddress);
        collateralConfig = ICollateralConfig(_collateralConfigAddress);
        troveManager = ITroveManager(_troveManagerAddress);
        activePool = IActivePool(_activePoolAddress);
        lusdToken = ILUSDToken(_lusdTokenAddress);
        sortedTroves = ISortedTroves(_sortedTrovesAddress);
        priceFeed = IPriceFeed(_priceFeedAddress);
        communityIssuance = ICommunityIssuance(_communityIssuanceAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L232-L278](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L232-L278)
````solidity
    function setAddresses(
...      
        borrowerOperationsAddress = _borrowerOperationsAddress;
        collateralConfig = ICollateralConfig(_collateralConfigAddress);
        activePool = IActivePool(_activePoolAddress);
        defaultPool = IDefaultPool(_defaultPoolAddress);
        stabilityPool = IStabilityPool(_stabilityPoolAddress);
        gasPoolAddress = _gasPoolAddress;
        collSurplusPool = ICollSurplusPool(_collSurplusPoolAddress);
        priceFeed = IPriceFeed(_priceFeedAddress);
        lusdToken = ILUSDToken(_lusdTokenAddress);
        sortedTroves = ISortedTroves(_sortedTrovesAddress);
        lqtyToken = IERC20(_lqtyTokenAddress);
        lqtyStaking = ILQTYStaking(_lqtyStakingAddress);
        redemptionHelper = IRedemptionHelper(_redemptionHelperAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L61-L75](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L61-L75)
````solidity
    function setAddresses
    (
...
        OathToken = IERC20(_oathTokenAddress);
        stabilityPoolAddress = _stabilityPoolAddress;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L66-L91](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L66-L91)
````solidity
    function setAddresses
    (
... 
        lqtyToken = IERC20(_lqtyTokenAddress);
        lusdToken = ILUSDToken(_lusdTokenAddress);
        troveManagerAddress = _troveManagerAddress;
        borrowerOperationsAddress = _borrowerOperationsAddress;
        activePoolAddress = _activePoolAddress;
        collateralConfig = ICollateralConfig(_collateralConfigAddress);
````

### Recommended Mitigation Steps
Add a timelock to the functions above.

# [N-03] Constants should be defined rather than using magic numbers

## Vulnerability details
It is bad practice to use numbers directly in code without explanation.
````solidity
9 results - 2 files

/ActivePool.sol
127:  require(_bps <= 10_000, "Invalid BPS value");

133:  require(_driftBps <= 500, "Exceeds max allowed value of 500 BPS");

145:  require(_treasurySplit + _SPSplit + _stakingSplit == 10_000, "Splits must add up to 10000 BPS");

258:  vars.percentOfFinalBal = vars.finalBalance == 0 ? uint256(-1) : vars.currentAllocated.mul(10_000).div(vars.finalBalance);

262:  vars.finalYieldingAmount = vars.finalBalance.mul(vars.yieldingPercentage).div(10_000);

291:  vars.treasurySplit = vars.profit.mul(yieldSplitTreasury).div(10_000);

296:  vars.stakingSplit = vars.profit.mul(yieldSplitStaking).div(10_000);

/ReaperVaultV2.sol
155:  require(_feeBPS <= PERCENT_DIVISOR / 5, "Fee cannot be higher than 20 BPS");

181:  require(_feeBPS <= PERCENT_DIVISOR / 5, "Fee cannot be higher than 20 BPS");
````
### Tools Used
VS Code
### Recommended Mitigation Steps
Consider storing the value after declaring a `constant`.

# [N-04] Interchangeable usage of `uint` and `uint256`
## Lines of code
Present in most files.

### Recommended Mitigation Steps
Consider using only one approach throughout the codebase, e.g. only `uint` or only `uint256`.
````solidity
  struct LocalVariables_adjustTrove {
-          uint256 collCCR;
-          uint256 collMCR;
-          uint256 collDecimals;
+          uint collCCR;
+          uint collMCR;
+          uint collDecimals;
          uint price; 
          uint collChange;
          uint netDebtChange;
          bool isCollIncrease;
          uint debt;
          uint coll;
          uint oldICR;
          uint newICR;
          uint newTCR;
          uint LUSDFee;
          uint newDebt;
          uint newColl;
          uint stake;
      }
````
or
````solidity
struct LocalVariables_adjustTrove { 
        uint256 collCCR;
        uint256 collMCR;
        uint256 collDecimals;
-        uint price; 
-        uint collChange;
-        uint netDebtChange;
+        uint price; 
+        uint collChange;
+        uint netDebtChange;
        bool isCollIncrease;
-        uint debt;
-        uint coll;
-        uint oldICR;
-        uint newICR;
-        uint newTCR;
-        uint LUSDFee;
-        uint newDebt;
-        uint newColl;
-        uint stake;
+        uint256 debt;
+        uint256 coll;
+        uint256 oldICR;
+        uint256 newICR;
+        uint256 newTCR;
+        uint256 LUSDFee;
+        uint256 newDebt;
+        uint256 newColl;
+        uint256 stake;
    }
````

# [N-05] Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L131](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L131)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L494](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L494)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L496](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L496)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1132](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1132)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1147](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1147)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1251-L1252](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1251-L1252)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L40](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L40)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L125](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L125)
## Vulnerability details
While the compiler knows to optimize away the exponentiation, it’s still better coding practice to use idioms that do not require compiler optimization, if they exist.   
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L131](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L131)  
````solidity
131:  vars.newColl = vars.collAmount.sub(vars.maxRedeemableLUSD.mul(10**vars.collDecimals).div(_price));
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L494](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L494)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L496](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L496)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1132](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1132)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1147](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1147)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1251-L1252](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1251-L1252)   
````solidity
494:  cappedCollPortion = cappedCollPortion.div(10 ** (LiquityMath.CR_CALCULATION_DECIMALS - _collDecimals));

496:  cappedCollPortion = cappedCollPortion.mul(10 ** (_collDecimals - LiquityMath.CR_CALCULATION_DECIMALS));

1132: uint pendingCollateralReward = stake.mul(rewardPerUnitStaked).div(10**collDecimals);

1147: uint pendingLUSDDebtReward = stake.mul(rewardPerUnitStaked).div(10**collDecimals);

1251: uint collateralNumerator = _coll.mul(10**_collDecimals).add(lastCollateralError_Redistribution[_collateral]);
1252: uint LUSDDebtNumerator = _debt.mul(10**_collDecimals).add(lastLUSDDebtError_Redistribution[_collateral]);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L40](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L40)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L125](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L125)
````solidity
40: uint256 public constant DEGRADATION_COEFFICIENT = 10**18; // The unit for calculating profit degradation.
125:  lockedProfitDegradation = (DEGRADATION_COEFFICIENT * 46) / 10**6; // 6 hours in blocks
````


# [N-06] Use of `bytes.concat()` instead of `abi.encodePacked()`

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L178](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L178)
## Vulnerability details
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L178](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L178)
````solidity
178:            latestRandomSeed = uint(keccak256(abi.encodePacked(latestRandomSeed)));
````
Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, `bytes.concat()` is enabled.     

Since version 0.8.4 for appending bytes, `bytes.concat()` can be used instead of `abi.encodePacked(,)`.


# [N-07] Signature Malleability of EVM’s `ecrecover()`

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L287](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L287)
## Vulnerability details
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L287](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L287)
````solidity
287:        address recoveredAddress = ecrecover(digest, v, r, s);
````
Description: The function calls the Solidity ecrecover() function directly to verify the given signatures. However, the ecrecover() EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.    

Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin’s ECDSA library.


### Recommended Mitigation Steps
Use the ecrecover function from OpenZeppelin’s ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

# [N-08] `2**<n> - 1` should be re-written as `type(uint<n>).max`

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1329](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1329)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1346](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1346)
## Vulnerability details
Earlier versions of solidity can use `uint<n>(-1)` instead. Expressions not including the - 1 can often be re-written to accomodate the change (e.g. by using a > rather than a >=, which will also save some gas).          
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1329](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1329)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1346](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1346)
````solidity
1329: index = uint128(TroveOwners[_collateral].length.sub(1));
1346: uint idxLast = length.sub(1);
````

# [N-09] Not using the latest version of OpenZeppelin from dependencies

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/package.json#L34](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/package.json#L34)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/package.json#L41-L42](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/package.json#L41-L42)
## Vulnerability details
The `package.json` configuration file says that the project is using 4.7.3 of OpenZeppelin which has a not last update version.     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/package.json#L34](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/package.json#L34)
````solidity
34: "@openzeppelin/contracts": "^3.3.0",
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/package.json#L41-L42](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/package.json#L41-L42)
````solidity
41: "@openzeppelin/contracts": "^4.7.3",
42: "@openzeppelin/contracts-upgradeable": "^4.7.3",
````
### Recommended Mitigation Steps
Use patched versions (4.8.1).
  
# [N-10] `constant` redefined elsewhere

## Vulnerability details
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A cheap way to store constants in a single location is to create an `internal constant` in a `library`.     
If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.
````solidity
12 results - 8 files

Ethos-Core/contracts/ActivePool.sol
30: string constant public NAME = "ActivePool";

Ethos-Core/contracts/BorrowerOperations.sol
21: string constant public NAME = "BorrowerOperations";

Ethos-Core/contracts/CollateralConfig.sol
21: uint256 constant public MIN_ALLOWED_MCR = 1.1 ether;
25: uint256 constant public MIN_ALLOWED_CCR = 1.5 ether;

Ethos-Core/contracts/HintHelpers.sol
13: string constant public NAME = "HintHelpers";

Ethos-Core/contracts/StabilityPool.sol
150:  string constant public NAME = "StabilityPool";
197:  uint public constant SCALE_FACTOR = 1e9;

Ethos-Core/contracts/TroveManager.sol
48: uint constant public SECONDS_IN_ONE_MINUTE = 60;

53: uint constant public MINUTE_DECAY_FACTOR = 999037758833783000;  
54: uint constant public override REDEMPTION_FEE_FLOOR = DECIMAL_PRECISION / 1000 * 5; // 0.5% 
55: uint constant public MAX_BORROWING_FEE = DECIMAL_PRECISION / 100 * 5; // 5%

61: uint constant public BETA = 2;

Ethos-Core/contracts/LQTY/CommunityIssuance.sol
19: string constant public NAME = "CommunityIssuance";

Ethos-Core/contracts/LQTY/LQTYStaking.sol
23: string constant public NAME = "LQTYStaking";
````
````solidity
19 results - 3 files
Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol
24: address public constant VELO_ROUTER = 0xa132DAB612dB5cB9fC9Ac426A0Cc215A3423F9c9;  
25: ILendingPoolAddressesProvider public constant ADDRESSES_PROVIDER =
26: ILendingPoolAddressesProvider(0xdDE5dC81e40799750B92079723Da2acAF9e1C6D6); 
27: IAaveProtocolDataProvider public constant DATA_PROVIDER =
28: IAaveProtocolDataProvider(0x9546F673eF71Ff666ae66d01Fd6E7C6Dae5a9995);  
29: IRewardsController public constant REWARDER = IRewardsController(0x6A0406B8103Ec68EE9A713A073C7bD587c5e04aD);
  
Ethos-Vault/contracts/ReaperVaultV2.sol
40: uint256 public constant DEGRADATION_COEFFICIENT = 10**18;
41: uint256 public constant P
  
73: bytes32 public constant DEPOSITOR = keccak256("DEPOSITOR");
74: bytes32 public constant STRATEGIST = keccak256("STRATEGIST");
75: bytes32 public constant GUARDIAN = keccak256("GUARDIAN");
76: bytes32 public constant ADMIN = keccak256("ADMIN");
  
Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol
23: uint256 public constant PERCENT_DIVISOR = 10_000;  
24: uint256 public constant UPGRADE_TIMELOCK = 48 hours; // minimum 48 hours for RF 
25: uint256 public constant FUTURE_NEXT_PROPOSAL_TIME = 365 days * 100;

49: bytes32 public constant KEEPER = keccak256("KEEPER");
50: bytes32 public constant STRATEGIST = keccak256("STRATEGIST");
51: bytes32 public constant GUARDIAN = keccak256("GUARDIAN");
52: bytes32 public constant ADMIN = keccak256("ADMIN")
```` 

# [N-11] Showing the actual values of numbers in NatSpec comments makes checking and reading code easier

## Vulnerability details
````solidity
1 result - 1 file
  
Ethos-Core/contracts/LQTY/CommunityIssuance.sol
58: distributionPeriod = 14 days;
````
### Recommended Mitigation Steps
````solidity  
Ethos-Core/contracts/LQTY/CommunityIssuance.sol
- 58: distributionPeriod = 14 days; 
+ 58: distributionPeriod = 14 days; // 1_209_600 ( 14 * 24 * 60 * 60)
````
  
# [N-12] Inconsistent Solidity Versions

## Vulnerability details
Different Solidity compiler versions are used throughout the Ethos protocol.    
To be more specific, the two main mappings (Ethos-Core and Ethos-Vault) mix solidity versions:
````solidity 
Ethos-Core/contracts/ActivePool.sol
3:  pragma solidity 0.6.11;

Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol
3:  pragma solidity ^0.8.0;
````

### Recommended Mitigation Steps
````solidity  
Versions must be consistent with each other.
````
  
# [N-13] Inconsistent spacing in contract

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L25](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L25)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L28](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L28)
## Vulnerability details
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L25](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L25)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L28](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L28)
````solidity 
25:     mapping( address => uint) public stakes;
28:     mapping (address => uint) public F_Collateral;  // Running sum of collateral fees per-LQTY-staked
````


# [N-14] Lack of event emission after critical `initialize()` function

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73)
## Vulnerability details
To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the `initialize()` function:
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73)
````solidity 
62:     function initialize(
63:         address _vault,
64:         address[] memory _strategists,
65:         address[] memory _multisigRoles,
66:         IAToken _gWant
67:     ) public initializer {
68:         gWant = _gWant;
69:         want = _gWant.UNDERLYING_ASSET_ADDRESS();
70:         __ReaperBaseStrategy_init(_vault, want, _strategists, _multisigRoles);
71:        rewardClaimingTokens = [address(_gWant)];
72:     }
````


# [N-15] Mark visibility of `initialize()` functions as `external`

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73)
## Vulnerability details
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L62-L73)
````solidity 
62:     function initialize(
63:         address _vault,
64:         address[] memory _strategists,
65:         address[] memory _multisigRoles,
66:         IAToken _gWant
67:     ) public initializer {
68:         gWant = _gWant;
69:         want = _gWant.UNDERLYING_ASSET_ADDRESS();
70:         __ReaperBaseStrategy_init(_vault, want, _strategists, _multisigRoles);
71:        rewardClaimingTokens = [address(_gWant)];
72:     }
````
If someone wants to extend via inheritance, it might make more sense that the overridden `initialize()` function calls the internal `{}_init` function, not the parent public `initialize()` function.    

External instead of public would give more sense of the `initialize()` functions to behave like a constructor (only called on deployment, so should only be called externally).    

From a security point of view, it might be safer so that it cannot be called internally by accident in the child contract.     

It might cost a bit less gas to use external over public.     

It is possible to override a function from external to public (= “opening it up”), but it is not possible to override a function from public to external (= “narrow it down”).

For the above reasons, you can change `initialize()` to external:     
[https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750)


# [N-16] Constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

## Vulnerability details
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.      

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.       

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the `constructor`. 
````solidity
8 results - 2 files

Ethos-Vault/contracts/ReaperVaultV2.sol
73	bytes32 public constant DEPOSITOR = keccak256("DEPOSITOR");
74	bytes32 public constant STRATEGIST = keccak256("STRATEGIST");
75	bytes32 public constant GUARDIAN = keccak256("GUARDIAN");
76:	bytes32 public constant ADMIN = keccak256("ADMIN");

Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol
49	bytes32 public constant KEEPER = keccak256("KEEPER");
50	bytes32 public constant STRATEGIST = keccak256("STRATEGIST");
51	bytes32 public constant GUARDIAN = keccak256("GUARDIAN");
52:	bytes32 public constant ADMIN = keccak256("ADMIN");
````



# [N-17] Assembly Codes Specific – Should Have Comments

## Vulnerability details
Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.    
     
This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.    
     
Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.     
````solidity
299        assembly {
300            chainID := chainid()
301:        }
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L299-L301](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L299-L301)



# [N-18] Use `require` instead of `assert`

## Vulnerability details
The Solidity `assert()` function is meant to assert invariants. Properly functioning code should never reach a failing assert statement.
````solidity
20 results - 4 files

Ethos-Core/contracts/BorrowerOperations.sol
128:  assert(MIN_NET_DEBT > 0);

197:  assert(vars.compositeDebt > 0);

301:  assert(msg.sender == _borrower || (msg.sender == stabilityPoolAddress && _collTopUp > 0 && _LUSDChange == 0));

331:  assert(_collWithdrawal <= vars.coll);

Ethos-Core/contracts/LUSDToken.sol
312:  assert(sender != address(0));
313:  assert(recipient != address(0));

321:  assert(account != address(0));

329:  assert(account != address(0));

337:  assert(owner != address(0));
338:  assert(spender != address(0));

Ethos-Core/contracts/StabilityPool.sol
526:  assert(_debtToOffset <= _totalLUSDDeposits);

551:  assert(_LUSDLossPerUnitStaked <= DECIMAL_PRECISION);

591:  assert(newP > 0);

Ethos-Core/contracts/TroveManager.sol
417:  assert(_LUSDInStabPool != 0);

1224: assert(totalStakesSnapshot[_collateral] > 0);

1279: assert(closedStatus != Status.nonExistent && closedStatus != Status.active);

1342: assert(troveStatus != Status.nonExistent && troveStatus != Status.active);

1348: assert(index <= idxLast);

1414: assert(newBaseRate > 0); // Base rate is always non-zero after redemption

1489: assert(decayedBaseRate <= DECIMAL_PRECISION);
````

### Recommended Mitigation Steps
Consider whether the condition checked in the `assert()` is actually an invariant. If not, replace the `assert()` statement with a `require()` statement.

# [N-19] NatSpec comments should be increased in contracts

## Vulnerability details
### Context
All Contracts

### Description
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.       
     
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. [https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

### Recommended Mitigation Steps
NatSpec comments should be increased in contracts.

# [N-20] `Function writing` that does not comply with the `Solidity Style Guide`

## Vulnerability details
### Context
All Contracts

### Description
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.      
      
[https://docs.soliditylang.org/en/v0.8.17/style-guide.html](https://docs.soliditylang.org/en/v0.8.17/style-guide.html)          
    
Functions should be grouped according to their visibility and ordered:       
      
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

# [N-21] Include return parameters in NatSpec comments

## Vulnerability details
### Context
All Contracts

### Description
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.        
      
[https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)      

### Recommended mitigaton steps
Include return parameters in NatSpec comments.     
      
Recommendation Code Style: (from Uniswap3)
````solidity
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
````
# [N-22] Long lines are not suitable for the `Solidity Style Guide`

## Vulnerability details
### Context
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L268](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L268)      
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L282](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L282)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L343](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L343)

### Description
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today’s screens are much larger, so in some cases it makes sense to expand that. The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.       
      
[why-is-80-characters-the-standard-limit-for-code-width](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width)
### Recommended mitigaton steps
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

[https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction)
````solidity
	thisFunctionCallIsReallyLong(
	    longArgument1,
	    longArgument2,
	    longArgument3
	);  
````
# [N-23] For functions, follow Solidity standard naming conventions (internal function style rule)

## Vulnerability details
### Context
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultERC4626.sol#L269](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultERC4626.sol#L269)

### Description
````solidity
269    	function roundUpDiv(uint256 x, uint256 y) internal pure returns (uint256) {
````
The above codes don’t follow Solidity’s standard naming convention,      
     
internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore).

# [I-01] `event` is not emitted when an important action happens on-chain

## Vulnerability details
### Proof of Concept
No `event` is emitted when an important action happens on-chain.
````solidity
17 results - 8 files
/ActivePool.sol
239:  function _rebalance(address _collateral, uint256 _amountLeavingPool) internal {

/BorrowerOperations.sol
476:  function _moveTokensAndCollateralfromAdjustment
    (
        IActivePool _activePool,
        ILUSDToken _lusdToken,
        address _borrower,
        address _collateral,
        uint _collChange,
        bool _isCollIncrease,
        uint _LUSDChange,
        bool _isDebtIncrease,
        uint _netDebtChange
    )
        internal
    {

511:  function _withdrawLUSD(IActivePool _activePool, ILUSDToken _lusdToken, address _collateral, address _account, uint _LUSDAmount, uint _netDebtIncrease) internal {

517:  function _repayLUSD(IActivePool _activePool, ILUSDToken _lusdToken, address _collateral, address _account, uint _LUSD) internal {

/LUSDToken.sol
133:  function pauseMinting() external {

141:  function unpauseMinting() external {

/StabilityPool.sol
794:  function _sendLUSDToDepositor(address _depositor, uint LUSDWithdrawal) internal {

/TroveManager.sol
1189: function _removeStake(address _borrower, address _collateral) internal {

1213: function _computeNewStake(address _collateral, uint _coll) internal view returns (uint) {

1278: function _closeTrove(address _borrower, address _collateral, Status closedStatus) internal {

/CommunityIssuance.sol
61: function setAddresses 
    (
        address _oathTokenAddress, 
        address _stabilityPoolAddress
    ) 
        external 
        onlyOwner
        override 
    {

84: function issueOath() external override returns (uint issuance) {

/ReaperVaultERC4626.sol
110:  function deposit(uint256 assets, address receiver) external override returns (uint256 shares) {

154:  function mint(uint256 shares, address receiver) external override returns (uint256 assets) {

202:  function withdraw(
        uint256 assets,
        address receiver,
        address owner
    ) external override returns (uint256 shares) {

258:  function redeem(
        uint256 shares,
        address receiver,
        address owner
    ) external override returns (uint256 assets) {

/ReaperVaultV2.sol
627:  function updateTreasury(address newTreasury) external {
````

### Recommended Mitigation Steps
Add Event-Emit.

# [R-01] Some number values can be refactored with `_`

## Vulnerability details
Consider using underscores for number values to improve readability.
````solidity 
Ethos-Core/contracts/TroveManager.sol
54:	uint constant public override REDEMPTION_FEE_FLOOR = DECIMAL_PRECISION / 1000 * 5; // 0.5%

Ethos-Vault/contracts/ReaperVaultV2.sol
41:	uint256 public constant PERCENT_DIVISOR = 10000;
````
The instances above can be refactored to:
````solidity 
Ethos-Core/contracts/TroveManager.sol
54:	uint constant public override REDEMPTION_FEE_FLOOR = DECIMAL_PRECISION / 1_000 * 5; // 0.5%

Ethos-Vault/contracts/ReaperVaultV2.sol
41:	uint256 public constant PERCENT_DIVISOR = 10_000;
````

# [O-01] Floating `pragma`

## Lines of code
All files in `Ethos-Vault` mapping.
## Vulnerability details
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the `pragma` helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.    
[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)
````solidity 
pragma solidity ^0.8.0;
````

### Recommended Mitigation Steps
Lock the `pragma` version and also consider known bugs [(https://github.com/ethereum/solidity/releases)](https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

# [O-02] Use a more recent version of Solidity

## Vulnerability details
For security, it is best practice to use the latest Solidity version.    

For the security fix list in the versions:   
[https://github.com/ethereum/solidity/blob/develop/Changelog.md](https://github.com/ethereum/solidity/blob/develop/Changelog.md)

### Recommended Mitigation Steps
Old version of Solidity is used, newer version can be used (`0.8.17`) .

# [S-01] Project Upgrade and Stop Scenario should be

## Vulnerability details
At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation. This can also be called an ” EMERGENCY STOP (CIRCUIT BREAKER) PATTERN “.      
       
[https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol](https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol)

# [S-02] Use descriptive names for Contracts and Libraries

## Vulnerability details
This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.       
       
Prefixes should be added like this by filing:      
      
Interface `I_`     
absctract contracts `Abs_`     
Libraries `Lib_`     
We recommend that you implement this or a similar agreement.

















# [G-01] `bytes constant` are more efficient than `string constant`

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L30](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L30)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L21](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L21)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L32-L34](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L32-L34)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L150](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L150)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L19](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L19)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L23](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L23)
## Vulnerability details
If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L30](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L30)
````solidity
	string constant public NAME = "ActivePool";
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L21](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L21) 
````solidity
	string constant public NAME = "BorrowerOperations";
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L32-L34](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L32-L34)
````solidity
	string constant internal _NAME = "LUSD Stablecoin";
	string constant internal _SYMBOL = "LUSD";
	string constant internal _VERSION = "1";
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L150](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L150) 
````solidity
	string constant public NAME = "StabilityPool";
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L19](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L19) 
````solidity
	string constant public NAME = "CommunityIssuance";
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L23](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L23)
````solidity
	string constant public NAME = "LQTYStaking";
````


# [G-02] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a `struct`, where appropriate

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L41-L47](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L41-L47)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L56-L64](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L56-L64)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L167](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L167)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L186-L187](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L186-L187)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L228](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L228)      
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L85-L120](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L85-L120)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L25-L32](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L25-L32)
## Vulnerability details
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.       
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L41-L47](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L41-L47)
````solidity
    mapping (address => uint256) internal collAmount;  // collateral => amount tracker
    mapping (address => uint256) internal LUSDDebt;  // collateral => corresponding debt tracker


    mapping (address => uint256) public yieldingPercentage; // collateral => % to use for yield farming (in BPS, <= 10k)
    mapping (address => uint256) public yieldingAmount; // collateral => actual wei amount being used for yield farming
    mapping (address => address) public yieldGenerator; // collateral => corresponding ERC4626 vault
    mapping (address => uint256) public yieldClaimThreshold; // collateral => minimum wei amount of yield to claim and redistribute
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L56-L64](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L56-L64) 
````solidity
    // User data for LUSD token
    mapping (address => uint256) private _balances;
    mapping (address => mapping (address => uint256)) private _allowances;  
    
    // --- Addresses ---
    // mappings store addresses of old versions so they can still burn (close troves)
    mapping (address => bool) public troveManagers;
    mapping (address => bool) public stabilityPools;
    mapping (address => bool) public borrowerOperations;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L167](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L167)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L186-L187](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L186-L187)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L228](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L228)
````solidity
    mapping (address => uint256) internal collAmounts;  // deposited collateral tracker
    
    mapping (address => Deposit) public deposits;  // depositor address -> Deposit struct
    mapping (address => Snapshots) public depositSnapshots;  // depositor address -> snapshots struct
    
    mapping (address => uint) public lastCollateralError_Offset;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L85-L120](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L85-L120)
````solidity
    // user => (collateral type => trove)
    mapping (address => mapping (address => Trove)) public Troves;


    mapping (address => uint) public totalStakes;


    // Snapshot of the value of totalStakes for each collateral, taken immediately after the latest liquidation
    mapping (address => uint) public totalStakesSnapshot;


    // Snapshot of the total collateral across the ActivePool and DefaultPool, immediately after the latest liquidation.
    mapping (address => uint) public totalCollateralSnapshot;


    /*
    * L_Collateral and L_LUSDDebt track the sums of accumulated liquidation rewards per unit staked. During its lifetime, each stake earns:
    *
    * A collateral gain of ( stake * [L_Collateral - L_Collateral(0)] )
    * A LUSDDebt increase  of ( stake * [L_LUSDDebt - L_LUSDDebt(0)] )
    *
    * Where L_Collateral(0) and L_LUSDDebt(0) are snapshots of L_Collateral and L_LUSDDebt for the active Trove taken at the instant the stake was made
    */
    mapping (address => uint) public L_Collateral;
    mapping (address => uint) public L_LUSDDebt;


    // Map addresses with active troves to their RewardSnapshot
    // user => (collateral type => reward snapshot))
    mapping (address => mapping (address => RewardSnapshot)) public rewardSnapshots;


    // Object containing the Collateral and LUSD snapshots for a given active trove
    struct RewardSnapshot { uint collAmount; uint LUSDDebt;}


    // Array of all active trove addresses - used to to compute an approximate hint off-chain, for the sorted list insertion
    // collateral type => array of trove owners
    mapping (address => address[]) public TroveOwners;


    // Error trackers for the trove redistribution calculation
    mapping (address => uint) public lastCollateralError_Redistribution;
    mapping (address => uint) public lastLUSDDebtError_Redistribution;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L25-L32](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L25-L32)
````solidity
    mapping( address => uint) public stakes;
    uint public totalLQTYStaked;


    mapping (address => uint) public F_Collateral;  // Running sum of collateral fees per-LQTY-staked
    uint public F_LUSD; // Running sum of LUSD fees per-LQTY-staked


    // User snapshots of F_Collateral and F_LUSD, taken at the point at which their latest deposit was made
    mapping (address => Snapshot) public snapshots; 
````


# [G-03] `require()` or `revert()` statements that check input arguments should be at the top of the function

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L93](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L93)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L107](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L107)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L127](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L127)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L97](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L97)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L754](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L754)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L197](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L197)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L81](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L81)
## Vulnerability details
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L93](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L93)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L107](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L107)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L127](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L127) 
````solidity
	require(_treasuryAddress != address(0), "Treasury cannot be 0 address");
	require(numCollaterals == _erc4626vaults.length, "Vaults array length must match number of collaterals");
	require(_bps <= 10_000, "Invalid BPS value");
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L97](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L97)
````solidity
	require(_CCR >= MIN_ALLOWED_CCR, "CCR below allowed minimum");
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L754](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L754)
````solidity
	require(totals.totalDebtInSequence > 0);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L197](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L197)
````solidity
	require(totalAllocBPS <= PERCENT_DIVISOR, "Invalid BPS value");
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L81](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L81)
````solidity
	require(_multisigRoles.length == 3, "Invalid number of multisig roles");
````

### Recommended mitigation steps
Consider moving on top of the `require` statements listed above.

# [G-04] Using `unchecked` blocks to save gas - Increments in `for` loop can be unchecked ( save 30-40 gas per loop iteration)

## Lines of code
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L108](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L108)      
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L56](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L56)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L351](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L351)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L397](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L397)      
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L640](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L640)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L810](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L810)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L831](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L831)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L859](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L859)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L608](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L608)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L690](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L690)      
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L808](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L808)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L882](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L882)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L206](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L206)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L228](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L228)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L240](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L240)
## Vulnerability details
### Impact
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.     
e.g Let’s work with a sample loop below.
````solidity
	for(uint256 i; i < 10; i++){
	//doSomething
	}
````
can be written as shown below.
````solidity
	for(uint256 i; i < 10;) {
	  // loop logic
	  unchecked { i++; }
	}
````
We can also write it as an inlined function like below.
````solidity
	function inc(i) internal pure returns (uint256) {
	  unchecked { return i + 1; }
	}
	for(uint256 i; i < 10; i = inc(i)) {
	  // doSomething
	}
````
### Proof of Concept
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L108](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L108)  
````solidity
108:	for(uint256 i = 0; i < numCollaterals; i++) {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L56](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L56)  
````solidity
56:	for(uint256 i = 0; i < _collaterals.length; i++) {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L351](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L351)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L397](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L397)      
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L640](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L640)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L810](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L810)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L831](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L831)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L859](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L859)    
````solidity
351:	for (uint i = 0; i < numCollaterals; i++) {

397:	for (uint i = 0; i < numCollaterals; i++) {

640:	for (uint i = 0; i < assets.length; i++) {

810:	for (uint i = 0; i < collaterals.length; i++) {

831:	for (uint i = 0; i < collaterals.length; i++) {

859:	for (uint i = 0; i < numCollaterals; i++) {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L608](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L608)     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L690](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L690)      
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L808](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L808)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L882](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L882)  
````solidity
608:	for (vars.i = 0; vars.i < _n && vars.user != firstUser; vars.i++) {

690:	for (vars.i = 0; vars.i < _n; vars.i++) {

808:	for (vars.i = 0; vars.i < _troveArray.length; vars.i++) {

882:	for (vars.i = 0; vars.i < _troveArray.length; vars.i++) {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L206](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L206)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L228](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L228)    
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L240](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L240)
````solidity
206:	for (uint i = 0; i < assets.length; i++) {

228:	for (uint i = 0; i < collaterals.length; i++) {

240:	for (uint i = 0; i < numCollaterals; i++) {
````


# [G-05] Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement

## Vulnerability details
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`    
There are 23 instances of this issue:     
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L266](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L266)
````solidity
266:	vars.toWithdraw = vars.currentAllocated.sub(vars.finalYieldingAmount);
267:	vars.yieldingAmount = vars.yieldingAmount.sub(vars.toWithdraw);

271:	vars.toDeposit = vars.finalYieldingAmount.sub(vars.currentAllocated);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L338](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L338)
````solidity
338:	_requireAtLeastMinNetDebt(_getNetDebt(vars.debt).sub(vars.netDebtChange));
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L465](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L465)
````solidity
465:	debtToRedistribute = _debt.sub(debtToOffset);
466:	collToRedistribute = _coll.sub(collToSendToSP);

888:	vars.remainingLUSDInStabPool = vars.remainingLUSDInStabPool.sub(singleLiquidation.debtToOffset);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L88](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L88)
````solidity
88:	uint256 timePassed = endTimestamp.sub(lastIssuanceTimestamp);

107:	uint timeLeft = lastDistributionTime.sub(lastIssuanceTimestamp);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L81](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L81)
````solidity
81:	uint256 toReinvest = wantBalance - _debt;

100:	loss = _amountNeeded - liquidatedAmount;

132:	uint256 profit = totalAssets - allocated;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L194](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L194)
````solidity
194:	totalAllocBPS -= strategies[_strategy].allocBPS;

244:	uint256 available = stratMaxAllocation - stratCurrentAllocation;

330:	_amount = balAfter - balBefore;

384:	uint256 remaining = value - vaultBalance;

390:	value -= loss;

421:	return lockedProfit - ((lockedFundsRatio * lockedProfit) / DEGRADATION_COEFFICIENT);

514:	strategy.allocated -= vars.debtPayment;
515:	totalAllocated -= vars.debtPayment;
516:	vars.debt -= vars.debtPayment;

535:	lockedProfit = vars.lockedProfitBeforeLoss - vars.loss;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L128](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L128)
````solidity
128:	repayment -= uint256(-roi);
````


# [G-06] `internal` functions only called once can be inlined to save gas

## Vulnerability details
Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.    
There are 57 instances of this issue:
````solidity
Ethos-Core/contracts/ActivePool.sol

320:	function _requireCallerIsBorrowerOperationsOrDefaultPool() internal view {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L320](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L320)
````solidity
Ethos-Core/contracts/BorrowerOperations.sol

438:	function _getCollChange(

455:	function _updateTroveFromAdjustment

476:	function _moveTokensAndCollateralfromAdjustment

533:	function _requireSingularCollChange(uint _collTopUp, uint _collWithdrawal) internal pure {

537:	function _requireNonZeroAdjustment(uint _collTopUp, uint _collWithdrawal, uint _LUSDChange) internal pure {

546:	function _requireTroveisNotActive(ITroveManager _troveManager, address _borrower, address _collateral) internal view {

551:	function _requireNonZeroDebtChange(uint _LUSDChange) internal pure {

555:	function _requireNotInRecoveryMode(

567:	function _requireNoCollWithdrawal(uint _collWithdrawal) internal pure {

571:	function _requireValidAdjustmentInCurrentMode

624:	function _requireNewICRisAboveOldICR(uint _newICR, uint _oldICR) internal pure {

636:	function _requireValidLUSDRepayment(uint _currentDebt, uint _debtRepayment) internal pure { 

640:	function _requireCallerIsStabilityPool() internal view {

661:	function _getNewNominalICRFromTroveChange

682:	function _getNewICRFromTroveChange
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L438](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L438)
````solidity
Ethos-Core/contracts/LUSDToken.sol

320:	function _mint(address account, uint256 amount) internal {

328:	function _burn(address account, uint256 amount) internal {

360:	function _requireCallerIsBorrowerOperations() internal view {

365:	function _requireCallerIsBOorTroveMorSP() internal view {

375:	function _requireCallerIsStabilityPool() internal view {

380:	function _requireCallerIsTroveMorSP() internal view {

393:	function _requireMintingNotPaused() internal view {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L320](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L320)
````solidity
Ethos-Core/contracts/StabilityPool.sol

421:	function _updateG(uint _LQTYIssuance) internal {

439:	function _computeLQTYPerUnitStaked(uint _LQTYIssuance, uint _totalLUSDDeposits) internal returns (uint) {

597:	function _moveOffsetCollAndDebt(address _collateral, uint _collToAdd, uint _debtToOffset) internal {

637:	function _getCollateralGainFromSnapshots(uint initialDeposit, Snapshots storage snapshots) internal view returns (address[] memory assets, uint[] memory amounts) {

693:	function _getLQTYGainFromSnapshots(uint initialDeposit, Snapshots memory snapshots) internal view returns (uint) {

729:	function _getCompoundedDepositFromSnapshots(

776:	function _sendLUSDtoStabilityPool(address _address, uint _amount) internal {

794:	function _sendLUSDToDepositor(address _depositor, uint LUSDWithdrawal) internal {

848:	function _requireCallerIsActivePool() internal view {

852:	function _requireCallerIsTroveManager() internal view {

856:	function _requireNoUnderCollateralizedTroves() internal {

869:	function _requireUserHasDeposit(uint _initialDeposit) internal pure {

873:	function _requireNonZeroAmount(uint _amount) internal pure {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L421](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L421)
````solidity
Ethos-Core/contracts/TroveManager.sol

478:	function _getCappedOffsetVals

582:	function _getTotalsFromLiquidateTrovesSequence_RecoveryMode

671:	function _getTotalsFromLiquidateTrovesSequence_NormalMode

785:	function _getTotalFromBatchLiquidate_RecoveryMode

864:	function _getTotalsFromBatchLiquidate_NormalMode

1082:	function _applyPendingRewards(IActivePool _activePool, IDefaultPool _defaultPool, address _borrower, address _collateral) internal {

1213:	function _computeNewStake(address _collateral, uint _coll) internal view returns (uint) {

1321:	function _addTroveOwnerToArray(address _borrower, address _collateral) internal returns (uint128 index) {

1339:	function _removeTroveOwner(address _borrower, address _collateral, uint TroveOwnersArrayLength) internal {

1538:	function _requireMoreThanOneTroveInSystem(uint TroveOwnersArrayLength, address _collateral) internal view {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L478](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L478)
````solidity
Ethos-Core/contracts/LQTY/LQTYStaking.sol

251:	function _requireCallerIsTroveManagerOrActivePool() internal view {

261:	function _requireCallerIsBorrowerOperations() internal view {

265:	function _requireUserHasStake(uint currentStake) internal pure {

269:	function _requireNonZeroAmount(uint _amount) internal pure {
````
[[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L128](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L128)](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L251)
````solidity
Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol

86:	function _liquidatePosition(uint256 _amountNeeded)

176:	function _deposit(uint256 toReinvest) internal {

206:	function _claimRewards() internal {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L86](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L86)
````solidity
Ethos-Vault/contracts/ReaperVaultV2.sol

462:	function _chargeFees(address strategy, uint256 gain) internal returns (uint256) {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L462](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L462)
````solidity
Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol

228:	function _adjustPosition(uint256 _debt) internal virtual;

240:	function _liquidatePosition(uint256 _amountNeeded)

250:	function _liquidateAllPositions() internal virtual returns (uint256 amountFreed);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L228](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L228)


# [G-07] Structs can be packed into fewer storage slots

## Vulnerability details
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct.      

Subsequent reads as well as writes have smaller gas savings.     

There are 8 instances of this issue:
````solidity
Ethos-Core/contracts/BorrowerOperations.sol

/// @audit Variable ordering with 15 slots instead of the current 16:
///	uint256(32):collCCR, uint256(32):collMCR, uint256(32):collMCR, uint(32):price, uint(32):collChange, uint(32):netDebtChange, uint(32):debt, uint(32):coll, uint(32):oldICR, uint(32):newICR, uint(32):newTCR, uint(32):LUSDFee, uint(32):newDebt, uint(32):newColl, uint(32):stake, bool(1):isCollIncrease 
47     struct LocalVariables_adjustTrove {
48        uint256 collCCR;
49        uint256 collMCR;
50        uint256 collDecimals;
51        uint price;
52        uint collChange;
53        uint netDebtChange;
54        bool isCollIncrease;
55        uint debt;
56        uint coll;
57        uint oldICR;
58        uint newICR;
59        uint newTCR;
60        uint LUSDFee;
61        uint newDebt;
62        uint newColl;
63        uint stake;
64:    }
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L47-L64](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L47-L64)
````solidity
Ethos-Core/contracts/CollateralConfig.sol

/// @audit Variable ordering with 3 slots instead of the current 4:
///	uint256(32):decimals, uint256(32):MCR, uint256(32):CCR, bool(1):allowed
27    struct Config {
28        bool allowed;
29        uint256 decimals;
30        uint256 MCR;
31        uint256 CCR;
32:    }
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L27-L32](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/CollateralConfig.sol#L27-L32)
````solidity
Ethos-Core/contracts/StabilityPool.sol

/// @audit Variable ordering with 4 slots instead of the current 5:
///	mapping(address => uint):S, uint128(16):scale, uint128(16):epoch, uint(256):P, uint(256):G
178    struct Snapshots {
179        mapping (address => uint) S;
180        uint P;
181        uint G;
182        uint128 scale;
183        uint128 epoch;
184:    }

/// @audit Variable ordering with 7 slots instead of the current 8:
///	uint128(16):epochSnapshot, uint128(16):scaleSnapshot, uint(32):P_Snapshot, uint(32):S_Snapshot, uint(32):firstPortion, uint(32):secondPortion, uint(32):gain, uint256(32):collDecimals 
646	struct LocalVariables_getSingularCollateralGain {
647		uint256 collDecimals;
648		uint128 epochSnapshot;
649		uint128 scaleSnapshot;
650		uint P_Snapshot;
651		uint S_Snapshot;
652		uint firstPortion;
653		uint secondPortion;
654		uint gain;
655:	    }
````
[[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L88](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L88)](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L178-L184)
````solidity
Ethos-Core/contracts/TroveManager.sol

/// @audit Variable ordering with 4 slots instead of the current 5:
///	user-defined(32):status, uint128(16):arrayIndex, uint(32):debt, uint(32):coll, uint(32):stake
77    struct Trove {
78        uint debt;
79        uint coll;
80        uint stake;
81        Status status;
82        uint128 arrayIndex;
83:    }

/// @audit Variable ordering with 7 slots instead of the current 8:
///	uint(32):price, uint(32):LUSDInStabPool, uint(32):liquidatedDebt, uint(32):liquidatedColl, uint256(32):collDecimals, uint256(32):collCCR, uint256(32):collMCR, bool(1):recoveryModeAtStart 
129	struct LocalVariables_OuterLiquidationFunction {
130		uint256 collDecimals;
131		uint256 collCCR;
132		uint256 collMCR;
133		uint price;
134		uint LUSDInStabPool;
135		bool recoveryModeAtStart;
136		uint liquidatedDebt;
137		uint liquidatedColl;
138:	    }

/// @audit Variable ordering with 10 slots instead of the current 11:
///	uint(32):remainingLUSDInStabPool, uint(32):i, uint(32):ICR, uint(32):TCR, uint(32):entireSystemDebt, uint(32):entireSystemColl, uint256(32):collDecimals, uint256(32):collCCR, uint256(32):collMCR, address(20):user, bool(1):backToNormalMode
146    struct LocalVariables_LiquidationSequence {
147        uint remainingLUSDInStabPool;
148        uint i;
149        uint ICR;
150        uint TCR;
151        address user;
152        bool backToNormalMode;
153        uint entireSystemDebt;
154        uint entireSystemColl;
155        uint256 collDecimals;
156        uint256 collCCR;
157        uint256 collMCR;
158:    }
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L77-L83](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L77-L83)
````solidity
Ethos-Vault/contracts/ReaperVaultV2.sol

/// @audit Variable ordering with 9 slots instead of the current 10:
///	uint256(32):loss, uint256(32):gain, uint256(32):fees, int256(32):available, uint256(32):debt, uint256(32):credit, uint256(32):debtPayment, uint256(32):freeWantInStrat, uint256(32):lockedProfitBeforeLoss, address(20):stratAddr
473    struct LocalVariables_report {
474        address stratAddr;
475        uint256 loss;
476        uint256 gain;
477        uint256 fees;
478        int256 available;
479        uint256 debt;
480        uint256 credit;
481        uint256 debtPayment;
482        uint256 freeWantInStrat;
483        uint256 lockedProfitBeforeLoss;
484:    }
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L473-L484](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L473-L484)



# [G-08] Using `storage` instead of `memory` for structs/arrays saves gas

## Vulnerability details
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array.     
If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.    

There are 4 instances of this issue:
````solidity
Ethos-Core/contracts/BorrowerOperations.sol

175:	ContractsCache memory contractsCache = ContractsCache(troveManager, activePool, lusdToken);

283:	ContractsCache memory contractsCache = ContractsCache(troveManager, activePool, lusdToken);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L175](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L175)
````solidity
Ethos-Core/contracts/StabilityPool.sol

687:	Snapshots memory snapshots = depositSnapshots[_depositor];

722:	Snapshots memory snapshots = depositSnapshots[_depositor];
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L687](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L687)



# [G-09] Splitting `require()` statements that use `&&` saves gas

## Vulnerability details
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas.  

There are 4 instances of this issue:
````solidity
Ethos-Core/contracts/BorrowerOperations.sol

653            require(_maxFeePercentage >= BORROWING_FEE_FLOOR && _maxFeePercentage <= DECIMAL_PRECISION,
654:                "Max fee percentage must be between 0.5% and 100%");
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L653-L654](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L653-L654)
````solidity
Ethos-Core/contracts/LUSDToken.sol

347        require(
348            _recipient != address(0) && 
349            _recipient != address(this),
350            "LUSD: Cannot transfer tokens directly to the LUSD token contract or the zero address"
351:        );
352        require(
353            !stabilityPools[_recipient] &&
354            !troveManagers[_recipient] &&
355            !borrowerOperations[_recipient],
356            "LUSD: Cannot transfer tokens directly to the StabilityPool, TroveManager or BorrowerOps"
357:        );
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L347-L357](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L347-L357)

````solidity
Ethos-Core/contracts/TroveManager.sol

1539:        require (TroveOwnersArrayLength > 1 && sortedTroves.getSize(_collateral) > 1);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1539](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1539)



# [G-10] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

## Vulnerability details
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)      
Each operation involving a `uint8` costs an extra [22-28 gas](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. The same goes for `uint16`/`uint128`.     
Use a larger size then downcast where needed.

There are 15 instances of this issue:
````solidity
Ethos-Core/contracts/LUSDToken.sol

35:	uint8 constant internal _DECIMALS = 18;

````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L35](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L35)
````solidity
Ethos-Core/contracts/StabilityPool.sol

200:	uint128 public currentScale;

203:	uint128 public currentEpoch;

558	uint128 currentScaleCached = currentScale;
559:	uint128 currentEpochCached = currentEpoch;

699	uint128 epochSnapshot = snapshots.epoch;
700:	uint128 scaleSnapshot = snapshots.scale;

738	uint128 scaleSnapshot = snapshots.scale;
739:	uint128 epochSnapshot = snapshots.epoch;

745:	uint128 scaleDiff = currentScale.sub(scaleSnapshot);

817	uint128 currentScaleCached = currentScale;
818:	uint128 currentEpochCached = currentEpoch;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L182-L183](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L182-L183)

````solidity
Ethos-Core/contracts/TroveManager.sol

1329:	index = uint128(TroveOwners[_collateral].length.sub(1));

1344:	uint128 index = Troves[_borrower][_collateral].arrayIndex;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1329](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1329)
````solidity
Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol

35:	uint16 private constant LENDER_REFERRAL_CODE_NONE = 0;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L35](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L35)


# [G-11] `public` functions not called by the contract should be declared `external` instead

## Vulnerability details
Contracts are allowed to override their parents’ functions and change the visibility from `external` to `public` and can save gas by doing so.     

`getNominalICR`
- [TroveManager.sol#L1045](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L1045)

`getPricePerFullShare`
- [ReaperVaultV2.sol#L295](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L295)



# [G-12] Using Solidity version `0.8.17` will provide an overall gas optimization

## Vulnerability details
Using at least `0.8.10` will save gas due to skipped `extcodesize` check if there is a return value. Currently the contracts are compiled using version `0.6.11`. It is easily changeable to `0.8.17` using the command `sed -i 's/0\.8\.7/^0.8.0/' test/*.sol && sed -i '4isolc = "0.8.17"' foundry.toml`.    



# [G-13] `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

## Vulnerability details
Using the addition operator instead of plus-equals saves gas.     

There are 22 instances of this issue:
````solidity
Ethos-Vault/contracts/ReaperVaultV2.sol

168:	totalAllocBPS += _allocBPS;

194:	totalAllocBPS -= strategies[_strategy].allocBPS;

196:	totalAllocBPS += _allocBPS;

214:	totalAllocBPS -= strategies[_strategy].allocBPS;

390	value -= loss;
391:	totalLoss += loss;

395:	strategies[stratAddr].allocated -= actualWithdrawn;
396:	totalAllocated -= actualWithdrawn;

444:	stratParams.allocBPS -= bpsChange;
445:	totalAllocBPS -= bpsChange;

450:	stratParams.losses += loss;
451:	stratParams.allocated -= loss; 
452:	totalAllocated -= loss;

505:	strategy.gains += vars.gain;

514	strategy.allocated -= vars.debtPayment;
515	totalAllocated -= vars.debtPayment;
516:	vars.debt -= vars.debtPayment;

520	strategy.allocated += vars.credit;
521:	totalAllocated += vars.credit;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L168](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L168)
````solidity
Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol

133:	toFree += profit;

141:	roi -= int256(loss);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L133](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol#L133)
````solidity
Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol

128:	repayment -= uint256(-roi);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L128](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L128)



# [G-14] `keccak256()` should only need to be called on a specific string literal once

## Vulnerability details
It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once.  

There are 3 instances of this issue:
````solidity
Ethos-Core/contracts/HintHelpers.sol

178:	latestRandomSeed = uint(keccak256(abi.encodePacked(latestRandomSeed)));
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L178](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L178)
````solidity
Ethos-Core/contracts/LUSDToken.sol

283        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', 
284                         domainSeparator(), keccak256(abi.encode(
285                         _PERMIT_TYPEHASH, owner, spender, amount, 
286:                         _nonces[owner]++, deadline))));

305:        return keccak256(abi.encode(typeHash, name, version, _chainID(), address(this)));
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L283-L286](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L283-L286)



# [G-15] Remove the `initializer` modifier

## Vulnerability details
If we can just ensure that the `initialize()` function could only be called from within the constructor, we shouldn’t need to worry about it getting called again.

````solidity
Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol

62	    function initialize(
63		address _vault,
64		address[] memory _strategists,
65		address[] memory _multisigRoles,
66		IAToken _gWant
67:	    ) public initializer {
````
In the EVM, the constructor’s job is actually to return the bytecode that will live at the contract’s address. So, while inside a constructor, your address `(address(this))` will be the deployment address, but there will be no bytecode at that address!     
So if we check `address(this).code.length` before the constructor has finished, even from within a delegatecall, we will get 0. So now let’s update our `initialize()` function to only run if we are inside a constructor:
````solidity
Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol

62	    function initialize(
63		address _vault,
64		address[] memory _strategists,
65		address[] memory _multisigRoles,
66		IAToken _gWant
67:	    ) public initializer {
+		require(address(this).code.length == 0, 'not in constructor');
````
Now the Proxy contract’s constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero.    

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version.



# [G-16] Use constants instead of `type(uintx).max`

## Vulnerability details
`type(uint256)` uses more gas in the distribution process and also for each transaction than constant usage.

````solidity
Ethos-Vault/contracts/ReaperVaultERC4626.sol

81:	if (tvlCap == type(uint256).max) return type(uint256).max;

124:	if (tvlCap == type(uint256).max) return type(uint256).max;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultERC4626.sol#L81](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultERC4626.sol#L81)
````solidity
Ethos-Vault/contracts/ReaperVaultV2.sol

588:	updateTvlCap(type(uint256).max);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L588](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L588)
````solidity
Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol

74:	IERC20Upgradeable(want).safeApprove(vault, type(uint256).max);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L74](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L74)

# [G-17] Use nested `if` and, avoid multiple check combinations

## Vulnerability details
Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.         
     
10 results - 3 files:

````solidity
Ethos-Core/contracts/ActivePool.sol
264:	if (vars.percentOfFinalBal > vars.yieldingPercentage && vars.percentOfFinalBal.sub(vars.yieldingPercentage) > yieldingPercentageDrift) {

269:	} else if(vars.percentOfFinalBal < vars.yieldingPercentage && vars.yieldingPercentage.sub(vars.percentOfFinalBal) > yieldingPercentageDrift) {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L264](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L264)
````solidity
Ethos-Core/contracts/BorrowerOperations.sol
311:	if (_isDebtIncrease && !isRecoveryMode) {

337:	if (!_isDebtIncrease && _LUSDChange > 0) {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L311](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/BorrowerOperations.sol#L311)
````solidity
Ethos-Core/contracts/TroveManager.sol
397:	} else if ((_ICR > _100pct) && (_ICR < _MCR)) {

415:	} else if ((_ICR >= _MCR) && (_ICR < _TCR) && (singleLiquidation.entireTroveDebt <= _LUSDInStabPool)) {

616:	if (vars.ICR >= vars.collMCR && vars.remainingLUSDInStabPool == 0) { break; }

651:	else if (vars.backToNormalMode && vars.ICR < vars.collMCR) {

817:	if (vars.ICR >= vars.collMCR && vars.remainingLUSDInStabPool == 0) { continue; }

853:	else if (vars.backToNormalMode && vars.ICR < vars.collMCR) {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L397](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L397)
### Recommended mitigation steps
````solidity
Ethos-Core/contracts/ActivePool.sol
- 264:	if (vars.percentOfFinalBal > vars.yieldingPercentage && vars.percentOfFinalBal.sub(vars.yieldingPercentage) > yieldingPercentageDrift) {
+ 264:	if (vars.percentOfFinalBal > vars.yieldingPercentage) {
+		if (vars.percentOfFinalBal.sub(vars.yieldingPercentage) > yieldingPercentageDrift) {
+		}
+	}
````

# [G-18] Setting the constructor to `payable`

## Vulnerability details
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of `msg.value == 0` and saves `13 gas` on deployment with no security risks.        
     
3 results - 3 files:

````solidity
Ethos-Core/contracts/LUSDToken.sol
84	    constructor
85	    ( 
86		address _troveManagerAddress,
87		address _stabilityPoolAddress,
88		address _borrowerOperationsAddress,
89		address _governanceAddress,
90		address _guardianAddress
91	    ) 
92		public 
93:	    {  
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L84-L93](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LUSDToken.sol#L84-L93)
````solidity
Ethos-Core/contracts/TroveManager.sol
225:    	constructor() public {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L225](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L225)
````solidity
Ethos-Vault/contracts/ReaperVaultV2.sol
111	    constructor(
112		address _token,
113		string memory _name,
114		string memory _symbol,
115		uint256 _tvlCap,
116		address _treasury,
117		address[] memory _strategists,
118		address[] memory _multisigRoles
119:	    ) ERC20(string(_name), string(_symbol)) {
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L111-L119](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L111-L119)
### Recommended mitigation steps
Set the constructor to `payable`.

# [G-19] Use `assembly` to write address storage values

## Vulnerability details
54 results - 9 files:

````solidity
Ethos-Core/contracts/ActivePool.sol
71	function setAddresses(
96:		collateralConfigAddress = _collateralConfigAddress;
97:		borrowerOperationsAddress = _borrowerOperationsAddress;
98:		troveManagerAddress = _troveManagerAddress;
99:		stabilityPoolAddress = _stabilityPoolAddress;
100:		defaultPoolAddress = _defaultPoolAddress;
101:		collSurplusPoolAddress = _collSurplusPoolAddress;
102:		treasuryAddress = _treasuryAddress;
103:		lqtyStakingAddress = _lqtyStakingAddress;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L96-L103](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/ActivePool.sol#L96-L103)
````solidity
Ethos-Core/contracts/HintHelpers.sol
27	function setAddresses(
39:		collateralConfig = ICollateralConfig(_collateralConfigAddress);
40:		sortedTroves = ISortedTroves(_sortedTrovesAddress);
41:		troveManager = ITroveManager(_troveManagerAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L39-L41](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/HintHelpers.sol#L39-L41)
````solidity
Ethos-Core/contracts/LUSDToken.sol
84	constructor
102:		troveManagerAddress = _troveManagerAddress;
106:		stabilityPoolAddress = _stabilityPoolAddress;        
110:		borrowerOperationsAddress = _borrowerOperationsAddress;       
114:		governanceAddress = _governanceAddress;        
117:		guardianAddress = _guardianAddress;

146	function updateGovernance(address _newGovernanceAddress) external {
149:		governanceAddress = _newGovernanceAddress;

153	function updateGuardian(address _newGuardianAddress) external {
156:		guardianAddress = _newGuardianAddress;

160	function upgradeProtocol(
170:		troveManagerAddress = _newTroveManagerAddress;		
174:		stabilityPoolAddress = _newStabilityPoolAddress;		
178:		borrowerOperationsAddress = _newBorrowerOperationsAddress;
````
````solidity
Ethos-Core/contracts/StabilityPool.sol
261	function setAddresses(
284:		borrowerOperations = IBorrowerOperations(_borrowerOperationsAddress);
285:		collateralConfig = ICollateralConfig(_collateralConfigAddress);
286:		troveManager = ITroveManager(_troveManagerAddress);
287:		activePool = IActivePool(_activePoolAddress);
288:		lusdToken = ILUSDToken(_lusdTokenAddress);
289:		sortedTroves = ISortedTroves(_sortedTrovesAddress);
290:		priceFeed = IPriceFeed(_priceFeedAddress);
291:		communityIssuance = ICommunityIssuance(_communityIssuanceAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L284-L291](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/StabilityPool.sol#L284-L291)
````solidity
Ethos-Core/contracts/TroveManager.sol
232		function setAddresses(
266:			borrowerOperationsAddress = _borrowerOperationsAddress;
267:			collateralConfig = ICollateralConfig(_collateralConfigAddress);
268:			activePool = IActivePool(_activePoolAddress);
269:			defaultPool = IDefaultPool(_defaultPoolAddress);
270:			stabilityPool = IStabilityPool(_stabilityPoolAddress);
271:			gasPoolAddress = _gasPoolAddress;
272:			collSurplusPool = ICollSurplusPool(_collSurplusPoolAddress);
273:			priceFeed = IPriceFeed(_priceFeedAddress);
274:			lusdToken = ILUSDToken(_lusdTokenAddress);
275:			sortedTroves = ISortedTroves(_sortedTrovesAddress);
276:			lqtyToken = IERC20(_lqtyTokenAddress);
277:			lqtyStaking = ILQTYStaking(_lqtyStakingAddress);
278:			redemptionHelper = IRedemptionHelper(_redemptionHelperAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L266-L278](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/TroveManager.sol#L266-L278)
````solidity
Ethos-Core/contracts/LQTY/CommunityIssuance.sol
261	function setAddresses
74:		OathToken = IERC20(_oathTokenAddress);
75:		stabilityPoolAddress = _stabilityPoolAddress;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L74-L75](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/CommunityIssuance.sol#L74-L75)
````solidity
Ethos-Core/contracts/LQTY/LQTYStaking.sol
66	function setAddresses
86:		lqtyToken = IERC20(_lqtyTokenAddress);
87:		lusdToken = ILUSDToken(_lusdTokenAddress);
88:		troveManagerAddress = _troveManagerAddress;
89:		borrowerOperationsAddress = _borrowerOperationsAddress;
90:		activePoolAddress = _activePoolAddress;
91:		collateralConfig = ICollateralConfig(_collateralConfigAddress);
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L86-L91](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/contracts/LQTY/LQTYStaking.sol#L86-L91)
````solidity
Ethos-Vault/contracts/ReaperVaultV2.sol
111	constructor(
120:		token = IERC20Metadata(_token);    
124:		treasury = _treasury;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L120](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/ReaperVaultV2.sol#L120)
````solidity
Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol
63	function __ReaperBaseStrategy_init(
72:		vault = _vault;
73:		want = _want;
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L72-L73](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol#L72-L73)
### Recommended mitigation steps
````solidity
Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol
	function __ReaperBaseStrategy_init(
		assembly{
			sstore(vault.store = _vault)
			sstore(want.store = _store)
		}
	}
````

# [G-20] Use a more recent version of solidity

## Vulnerability details
In `0.8.15` the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.     
     
In `0.8.17` prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.        
````solidity
Ethos-Core/contracts/CollateralConfig.sol
3:	pragma solidity 0.6.11;

Ethos-Core/contracts/BorrowerOperations.sol
3:	pragma solidity 0.6.11;

Ethos-Core/contracts/TroveManager.sol
3:	pragma solidity 0.6.11;

Ethos-Core/contracts/ActivePool.sol
3:	pragma solidity 0.6.11;

Ethos-Core/contracts/StabilityPool.sol
3:	pragma solidity 0.6.11;

Ethos-Core/contracts/LQTY/CommunityIssuance.sol
3:	pragma solidity 0.6.11;

Ethos-Core/contracts/LQTY/LQTYStaking.sol
3:	pragma solidity 0.6.11;

Ethos-Core/contracts/LUSDToken.sol
3:	pragma solidity 0.6.11;

Ethos-Vault/contracts/ReaperVaultV2.sol
3:	pragma solidity ^0.8.0;

Ethos-Vault/contracts/ReaperVaultERC4626.sol
3:	pragma solidity ^0.8.0;

Ethos-Vault/contracts/abstract/ReaperBaseStrategyV4.sol
3:	pragma solidity ^0.8.0;

Ethos-Vault/contracts/ReaperStrategyGranarySupplyOnly.sol
3:	pragma solidity ^0.8.0;

````

# [G-21] Upgrade Solidity’s `optimizer`

## Vulnerability details
Make sure Solidity’s `optimizer` is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity `optimizer` at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the `optimizer` to a high number.     
     
Set the optimization value higher than `800` in your `hardhat.config.js` file.
````solidity
38	    solidity: {
39		compilers: [
40		    {
41			version: "0.4.23",
42			settings: {
43			    optimizer: {
44				enabled: true,
45				runs: 100
46			    }
47			}
48		    },
49		    {
50			version: "0.5.17",
51			settings: {
52			    optimizer: {
53				enabled: true,
54				runs: 100
55			    }
56			}
57		    },
58		    {
59			version: "0.6.11",
60			settings: {
61			    optimizer: {
62				enabled: true,
63				runs: 100
64			    }
65			}
66		    },
67		]
68	    },
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/hardhat.config.js#L38-L68](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Core/hardhat.config.js#L38-L68)
````solidity
11	  solidity: {
12	    compilers: [
13	      {
14		version: "0.8.11",
15		settings: {
16		  optimizer: {
17		    enabled: true,
18		    runs: 200,
19		  },
20		},
21	      },
22	    ],
23:	  },
````
[https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/hardhat.config.js#L11-L23](https://github.com/code-423n4/2023-02-ethos/blob/main/Ethos-Vault/hardhat.config.js#L11-L23)

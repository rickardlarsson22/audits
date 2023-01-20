# OpenSea Seaport 1.2
# QA report
# Informational Findings (5)
## [INFO-01] Unlocked solidity pragmas
### Context
Present in most files.  
### Description
Most of the implementation code uses a solidity pragma of ^0.8.17. Unlocked solidity pragmas can result in unexpected behaviours or errors with different compiler versions.
```
pragma solidity ^0.8.17;
```
### Recommendation
Lock the compiler version on the affected contracts.
## [INFO-02] Use custom imports instead of the plain `import "Contract.sol"`
### Context
[AmountDeriver.sol#L8](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/AmountDeriver.sol#L8), [Assertions.sol#L6](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/Assertions.sol#L6), [BasicOrderFulfiller.sol#L23](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/BasicOrderFulfiller.sol#L23), [Consideration.sol#L23](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/Consideration.sol#L23), [Consideration.sol#L25](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/Consideration.sol#L25), [ConsiderationBase.sol#L12](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationBase.sol#L12), [ConsiderationDecoder.sol#L20](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationDecoder.sol#L20), [ConsiderationDecoder.sol#L22](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationDecoder.sol#L22), [ConsiderationEncoder.sol#L4](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationEncoder.sol#L4), [ConsiderationEncoder.sol#L20](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationEncoder.sol#L20), [ConsiderationErrors.sol#L6](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationErrors.sol#L6), [CounterManager.sol#L10](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/CounterManager.sol#L10), [CriteriaResolution.sol#L15](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/CriteriaResolution.sol#L15), [CriteriaResolution.sol#L17](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/CriteriaResolution.sol#L17), [Executor.sol#L16](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/Executor.sol#L16), [Executor.sol#L18](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/Executor.sol#L18), [FulfillmentApplier.sol#L16](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/FulfillmentApplier.sol#L16), [GettersAndDerivers.sol#L8](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/GettersAndDerivers.sol#L8), [LowLevelHelpers.sol#L4](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/LowLevelHelpers.sol#L4), [OrderCombiner.sol#L23](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L23), [OrderFulfiller.sol#L23](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderFulfiller.sol#L23), [OrderValidator.sol#L19](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderValidator.sol#L19), [ReentrancyGuard.sol#L8](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ReentrancyGuard.sol#L8), [SignatureVerification.sol#L12](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/SignatureVerification.sol#L12), [TokenTransferrer.sol#L4](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/TokenTransferrer.sol#L4), [Verifiers.sol#L10](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/Verifiers.sol#L10), [ZoneInteraction.sol#L16](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ZoneInteraction.sol#L16), [ReferenceAssertions.sol#L14](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceAssertions.sol#L14), [ReferenceBasicOrderFulfiller.sol#L28](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceBasicOrderFulfiller.sol#L28), [ReferenceExecutor.sol#L29](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceExecutor.sol#L29), [ReferenceFulfillmentApplier.sol#L22](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceFulfillmentApplier.sol#L22), [ReferenceGettersAndDerivers.sol#L12](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceGettersAndDerivers.sol#L12), [ReferenceOrderCombiner.sol#L34](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceOrderCombiner.sol#L34), [ReferenceOrderFulfiller.sol#L34](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceOrderFulfiller.sol#L34), [ReferenceReentrancyGuard.sol#L8](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceReentrancyGuard.sol#L8), [ReferenceSignatureVerification.sol#L12](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceSignatureVerification.sol#L12), [ReferenceTokenTransferrer.sol#L4](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceTokenTransferrer.sol#L4), [ReferenceZoneInteraction.sol#L29](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceZoneInteraction.sol#L29)
### Recommendation
```
-   import "./../Contract.sol";
+   import {Contract} from "./../Contract.sol";
```
## [INFO-03] Missing or Incomplete NatSpec
### Context
[ConduitEnums.sol](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/conduit/lib/ConduitEnums.sol), [ConduitStructs.sol](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/conduit/lib/ConduitStructs.sol), [PointerLibraries.sol](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/helpers/PointerLibraries.sol), [AbridgedTokenInterfaces.sol](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/AbridgedTokenInterfaces.sol), [ConduitControllerInterface.sol#L292-L296](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/ConduitControllerInterface.sol#L292-L296), [ContractOffererInterface.sol](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/ContractOffererInterface.sol), [EIP1271Interface.sol](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/EIP1271Interface.sol), [IERC721Receiver.sol](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/IERC721Receiver.sol), [SeaportInterface.sol#L447-L449](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/SeaportInterface.sol#L447-L449), [TransferHelperErrors.sol#L26](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/TransferHelperErrors.sol#L26), [TransferHelperErrors.sol#L32-L37](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/TransferHelperErrors.sol#L32-L37), [TransferHelperErrors.sol#L43-L48](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/TransferHelperErrors.sol#L43-L48), [TransferHelperErrors.sol#L64](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/TransferHelperErrors.sol#L64), [TransferHelperErrors.sol#L70-L74](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/TransferHelperErrors.sol#L70-L74), [TransferHelperErrors.sol#L80-L84](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/TransferHelperErrors.sol#L80-L84)
### Description
Some functions are missing @notive/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpe is an important part of code documentation, this affects code comprehension, auditability and usability.
### Recommendation
Consider adding in full NatSpec comments to all funtions and contracts to have complete code documentation for future use.
## [INFO-04] Event is missing indexed fields
### Context
[ConduitControllerInterface.sol#L29](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/ConduitControllerInterface.sol#L29), [ConsiderationEventsAndErrors.sol#L61](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/ConsiderationEventsAndErrors.sol#L61), [ConsiderationEventsAndErrors.sol#L69](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/interfaces/ConsiderationEventsAndErrors.sol#L69)
### Description
Index `event` fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields).
### Recommendation
Add index on the event.  
contracts/interfaces/ConduitControllerInterface.sol
```
-   event NewConduit(address conduit, bytes32 conduitKey);
+   event NewConduit(address indexed conduit, bytes32 conduitKey);
```
contracts/interfaces/ConsiderationEventsAndErrors.sol
```
-   event OrderValidated(bytes32 orderHash, OrderParameters orderParameters);
+   event OrderValidated(bytes32 indexed orderHash, OrderParameters orderParameters);

-   event OrdersMatched(bytes32[] orderHashes);
+   event OrdersMatched(bytes32[] indexed orderHashes);
```
## [INFO-05] Use delete
### Context
[OrderCombiner.sol#L239](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L239), [OrderCombiner.sol#L258](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L258), [ReferenceOrderCombiner.sol#L230](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceOrderCombiner.sol#L230), [ReferenceOrderCombiner.sol#L233](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceOrderCombiner.sol#L233), [ReferenceOrderCombiner.sol#L249](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceOrderCombiner.sol#L249), [ReferenceOrderCombiner.sol#L252](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/reference/lib/ReferenceOrderCombiner.sol#L252)
### Description
The specified contract's functions clear values by setting them to `= 0`.
### Recommendation
Consider changing the code by using `delete`.  
  
OrderCombiner.sol
```
-    advancedOrder.numerator = 0;
+    delete advancedOrder.numerator;
```
ReferenceOrderCombiner.sol
```
    if (maximumFulfilled == 0) {
                    // Mark fill fraction as zero as the order will not be used.
-                    advancedOrder.numerator = 0;
+                    delete advancedOrder.numerator;

                    // Mark fill fraction as zero as the order will not be used.
-                    orderToExecute.numerator = 0;
+                    delete orderToExecute.numerator;

                    // Continue iterating through the remaining orders.
                    continue;
                }

    if (numerator == 0) {
                    // Mark fill fraction as zero if the order is not fulfilled.
-                    advancedOrder.numerator = 0;
+                    delete advancedOrder.numerator;

                    // Mark fill fraction as zero as the order will not be used.
-                    orderToExecute.numerator = 0;
+                    delete orderToExecute.numerator;

                    // Continue iterating through the remaining orders.
                    continue;
                }
```            

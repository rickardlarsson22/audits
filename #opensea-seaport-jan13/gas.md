# OpenSea Seaport 1.2
# Gas optimizations report (2)
## [G-01] Unchecked{++i} is more efficient than i++
### Context
[OrderValidator.sol#L508](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderValidator.sol#L508)
### Description
The function `_getGeneratedOrder` uses `_contractNonces[offerer]++` which costs more gas than `++_contractNonces[offerer]`
### Recommendation
Use `++_contractNonces[offerer]` to increment the value of an uint variable
```
function _getGeneratedOrder(
    ...
    uint256 contractNonce;
                unchecked {
                    // Note: nonce will be incremented even for skipped orders,
                    // and  even if generateOrder's return data does not satisfy
                    // all the constraints. This is the case when errorBuffer
                    // !=0 and revertOnInvalid == false.
-                    contractNonce = _contractNonces[offerer]++;
+                    contractNonce = ++_contractNonces[offerer];
                }
    ...
    }
```
## [G-02] Removing initialization of loop counter if the value is 0 by default
### Context
[Conduit.sol#L99](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/conduit/Conduit.sol#L99), [Conduit.sol#L162](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/conduit/Conduit.sol#L162), [BasicOrderFulfiller.sol#L939](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/BasicOrderFulfiller.sol#L939), [BasicOrderFulfiller.sol#L1079](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/BasicOrderFulfiller.sol#L1079), [ConsiderationDecoder.sol#L399](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationDecoder.sol#L399), [ConsiderationDecoder.sol#L498](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationDecoder.sol#L498), [ConsiderationDecoder.sol#L538](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationDecoder.sol#L538), [ConsiderationDecoder.sol#L630](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationDecoder.sol#L630), [ConsiderationDecoder.sol#L673](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationDecoder.sol#L673), [ConsiderationDecoder.sol#L744](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/ConsiderationDecoder.sol#L744), [CriteriaResolution.sol#L57](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/CriteriaResolution.sol#L57), [TypehashDirectory.sol#L48](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/TypehashDirectory.sol#L48), [CriteriaResolution.sol#L141](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/CriteriaResolution.sol#L141), [CriteriaResolution.sol#L159](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/CriteriaResolution.sol#L159), [OrderCombiner.sol#L298](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L298), [OrderCombiner.sol#L356](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L356), [OrderCombiner.sol#L567](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L567), [OrderCombiner.sol#L596](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L596), [OrderCombiner.sol#L698](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L698), [OrderCombiner.sol#L736](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L736), [OrderCombiner.sol#L762](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L762), [OrderCombiner.sol#L799](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L799), [OrderCombiner.sol#L999](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderCombiner.sol#L999), [OrderFulfiller.sol#L207](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderFulfiller.sol#L207), [OrderFulfiller.sol#L301](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderFulfiller.sol#L301), [OrderValidator.sol#L549](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderValidator.sol#L549), [OrderValidator.sol#L591](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderValidator.sol#L591),[OrderValidator.sol#L667](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderValidator.sol#L667),[OrderValidator.sol#L753](https://github.com/ProjectOpenSea/seaport/blob/5de7302bc773d9821ba4759e47fc981680911ea0/contracts/lib/OrderValidator.sol#L753),
### Description
The initialization of `i = 0` can be skipped, as 0 is the default value.
### Recommendation
Skip initialization to 0.  
  
Conduit.sol
```
-       for (uint256 i = 0; i < totalStandardTransfers; ) {
+       for (uint256 i; i < totalStandardTransfers; ) {
```
BasicOrderFulfiller.sol
```
-        for (
                        uint256 i = 0; // @audit : gas - Removing initialization of loop counter if the value is 0 by default.
                        i < totalAdditionalRecipientsDataSize;
                        i += AdditionalRecipient_size
                    ) {
                    
+        for (
                        uint256 i; // @audit : gas - Removing initialization of loop counter if the value is 0 by default.
                        i < totalAdditionalRecipientsDataSize;
                        i += AdditionalRecipient_size
                    ) {

-        for (uint256 i = 0; i < totalAdditionalRecipientsDataSize; ) { 
+        for (uint256 i; i < totalAdditionalRecipientsDataSize; ) { 
```
ConsiderationDecoder.sol
```
-        for (uint256 offset = 0; offset < tailOffset; offset += OneWord) {
+        for (uint256 offset; offset < tailOffset; offset += OneWord) {
```
CriteriaResolution.sol
```
-        for (uint256 i = 0; i < totalCriteriaResolvers; ++i) {
+        for (uint256 i; i < totalCriteriaResolvers; ++i) {

-        for (uint256 i = 0; i < totalAdvancedOrders; ++i) {
+        for (uint256 i; i < totalAdvancedOrders; ++i) {

-        for (uint256 j = 0; j < totalItems; ++j) { 
+        for (uint256 j; j < totalItems; ++j) { 
```
OrderCombiner.sol
```
-        for (uint256 j = 0; j < totalOfferItems; ++j) {
+        for (uint256 j; j < totalOfferItems; ++j) {

-        for (uint256 j = 0; j < totalConsiderationItems; ++j) {
+        for (uint256 j; j < totalConsiderationItems; ++j) {

-        for (uint256 i = 0; i < totalOfferFulfillments; ) { 
+        for (uint256 i; i < totalOfferFulfillments; ) { 

-        for (uint256 i = 0; i < totalConsiderationFulfillments; ) {
+        for (uint256 i; i < totalConsiderationFulfillments; ) {

-        for (uint256 i = 0; i < totalExecutions; ) {
+        for (uint256 i; i < totalExecutions; ) {

-        for (uint256 i = 0; i < totalOrders; ++i) {
+        for (uint256 i; i < totalOrders; ++i) {

-        for (uint256 i = 0; i < totalFulfillments; ++i) {
+        for (uint256 i; i < totalFulfillments; ++i) {
```
OrderFulfiller.sol
```
-        for (uint256 i = 0; i < totalOfferItems; ++i) {
+        for (uint256 i; i < totalOfferItems; ++i) {

-        for (uint256 i = 0; i < totalConsiderationItems; ++i) {
+        for (uint256 i; i < totalConsiderationItems; ++i) {
```
OrderValidator.sol
```
-        for (uint256 i = 0; i < originalOfferLength; ) {
+        for (uint256 i; i < originalOfferLength; ) {

-        for (uint256 i = 0; i < newConsiderationLength; ) {
+        for (uint256 i; i < newConsiderationLength; ) {

-        for (uint256 i = 0; i < totalOrders; ) {
+        for (uint256 i; i < totalOrders; ) {

-        for (uint256 i = 0; i < totalOrders; ++i) {
+        for (uint256 i; i < totalOrders; ++i) {
```
TypeHashDirectory.sol
```
-        for (uint256 i = 0; i < MaxTreeHeight; ) {
+        for (uint256 i; i < MaxTreeHeight; ) {
```

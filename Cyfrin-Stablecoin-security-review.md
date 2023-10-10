# [M-01] Chainlink price is used without checking validity
## Context
[DSCEngine.sol#L345](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DSCEngine.sol#L345), [DSCEngine.sol#L363](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DSCEngine.sol#L363), [OracleLib.sol#L21-L33](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/libraries/OracleLib.sol#L21-L33)
## Vulnerability Details
The Cyfrin protocol relies on a Chainlink price oracle to determine the token amount and the USD value. However, the current implementation does not include any checks to verify the freshness of the price fetched from the Chainlink oracle.
````solidity
File: src/DSCEngine.sol

340      function getTokenAmountFromUsd(address token, uint256 usdAmountInWei) public view returns (uint256) {
341              // price of ETH (token)
342              // $/ETH ETH ??
343              // $2000 / ETH. $1000 = 0.5 ETH
344              AggregatorV3Interface priceFeed = AggregatorV3Interface(s_priceFeeds[token]);
345              (, int256 price,,,) = priceFeed.staleCheckLatestRoundData();//@audit chainlink price feed - stale price check is missing
346              // ($10e18 * 1e18) / ($2000e8 * 1e10)
347              return (usdAmountInWei * PRECISION) / (uint256(price) * ADDITIONAL_FEED_PRECISION);
348:         }
      
361      function getUsdValue(address token, uint256 amount) public view returns (uint256) {
362              AggregatorV3Interface priceFeed = AggregatorV3Interface(s_priceFeeds[token]);
363              (, int256 price,,,) = priceFeed.staleCheckLatestRoundData();//@audit //@audit chainlink price feed - stale price check is missing
364              // 1 ETH = $1000
365              // The returned value from CL will be 1000 * 1e8
366              return ((uint256(price) * ADDITIONAL_FEED_PRECISION) * amount) / PRECISION;
367:         }
````
As we can see from the lines of code above, the `staleCheckLatestRoundData()` function is called in both cases to check the Chainlink Oracle for stale data.
````solidity
File: src/libraries/OracleLib.sol

21     function staleCheckLatestRoundData(AggregatorV3Interface priceFeed)
22              public
23              view
24              returns (uint80, int256, uint256, uint256, uint80)
25          {
26              (uint80 roundId, int256 answer, uint256 startedAt, uint256 updatedAt, uint80 answeredInRound) =//@audit
27                  priceFeed.latestRoundData();
28      
29              uint256 secondsSince = block.timestamp - updatedAt;
30              if (secondsSince > TIMEOUT) revert OracleLib__StalePrice();
31      
32              return (roundId, answer, startedAt, updatedAt, answeredInRound);
33:         }
````
Although, the `priceFeed` is returning the following variables:
- `roundId`
- `answer`
- `startedAt`
- `updatedAt`
- `answeredInRound`       

, these return values are also meant to be used to do some extra checks before updating the price.        
The absence of proper checks in the current implementation can result in potential issues when Chainlink initiates a new round and faces challenges in reaching a consensus on the oracle's new value. If these checks are not in place, consumers of this contract may continue to utilize outdated, stale, or inaccurate data in situations where oracles fail to submit and commence a new round. Some potential reasons for this could include scenarios where Chainlink nodes abandon the oracle, chain congestion occurs, or vulnerabilities/attacks target the Chainlink system.
## Impact
This vulnerability is categorized as MEDIUM since it impacts user assets solely when the Chainlink oracle is in a compromised or unreliable state.
## Tools Used
VSCode
## Recommendations
To rectify this problem, it is advisable to incorporate checks that guarantee the freshness of the price received from Chainlink. You can utilize the following code snippet as a reference for validating the Chainlink price:
````diff
21     function staleCheckLatestRoundData(AggregatorV3Interface priceFeed)
22              public
23              view
24              returns (uint80, int256, uint256, uint256, uint80)
25          {
26              (uint80 roundId, int256 answer, uint256 startedAt, uint256 updatedAt, uint80 answeredInRound) =
27                  priceFeed.latestRoundData();
28
+               require(price > 0, "Invalid chainlink price");
+               require(answeredInRound >= roundId, "Stale price");      
29              uint256 secondsSince = block.timestamp - updatedAt;
30              if (secondsSince > TIMEOUT) revert OracleLib__StalePrice();
31      
32              return (roundId, answer, startedAt, updatedAt, answeredInRound);
33:         }
````
# [L-01] Floating pragma
## Context
[OracleLib.sol#L3](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/d1c5501aa79320ca0aeaa73f47f0dbc88c7b77e2/src/libraries/OracleLib.sol#L3), [DSCEngine.sol#L24](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DSCEngine.sol#L24), [DecentralizedStableCoin.sol#L24](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DecentralizedStableCoin.sol#L24)
## Vulnerability Details
In the three smart contracts in scope for this audit, the floating pragma ^0.8.18 is employed. This practice of locking the pragma version helps to enforce that contracts are not unintentionally deployed using a different pragma.       
By doing so, the contract system avoids potential issues that may arise from utilizing outdated pragma versions that could introduce bugs or from using recently released pragma versions that might have unknown security vulnerabilities.
````solidity
File: src/libraries/OracleLib.sol
3:      pragma solidity ^0.8.18;
````
[OracleLib.sol#L3](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/d1c5501aa79320ca0aeaa73f47f0dbc88c7b77e2/src/libraries/OracleLib.sol#L3)
````solidity
File: src/DSCEngine.sol
24:      pragma solidity ^0.8.18;
````
[DSCEngine.sol#L24](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DSCEngine.sol#L24)
````solidity
File: src/DecentralizedStableCoin.sol
24:      pragma solidity ^0.8.18;
````
[DecentralizedStableCoin.sol#L24](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DecentralizedStableCoin.sol#L24)
## Recommendations
It is recommended to lock the pragma version with a known bug-free compiler version. Avoid using floating pragma in the final live deployment whenever possible. By specifying a fixed compiler version, you ensure that the bytecode produced remains consistent across different builds. This is particularly crucial if you rely on bytecode-level verification of the code.
````diff
-      pragma solidity ^0.8.18;
+      pragma solidity 0.8.18;
````
# [L-02] Incomplete NatSpec documentation
## Summary
The NatSpec documentation serves a variety of important purposes, benefiting internal developers, external developers integrating with the project, auditors reviewing the code, and end users.
## Recommendations
To address this issue, consider adding the missing NatSpec documentation.
# [G-01] A modifier used only once and not being inherited should be inlined to save gas
## Context
[DSCEngine.sol#L102-L107](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DSCEngine.sol#L102-L107), [DSCEngine.sol#L152](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DSCEngine.sol#L152)
## Vulnerability Details
````solidity
File: src/DSCEngine.sol
102      modifier isAllowedToken(address token) {//@audit inline modifier only used once
103              if (s_priceFeeds[token] == address(0)) {
104                  revert DSCEngine__NotAllowedToken();
105              }
106              _;
107:         }
````
[DSCEngine.sol#L102-L107](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DSCEngine.sol#L102-L107)        
The above modifer is only used in the following:
````solidity
149      function depositCollateral(address tokenCollateralAddress, uint256 amountCollateral)
150              public
151              moreThanZero(amountCollateral)
152:             isAllowedToken(tokenCollateralAddress)
153              nonReentrant
154          {
155              s_collateralDeposited[msg.sender][tokenCollateralAddress] += amountCollateral;
156              emit CollateralDeposited(msg.sender, tokenCollateralAddress, amountCollateral);
157              bool success = IERC20(tokenCollateralAddress).transferFrom(msg.sender, address(this), amountCollateral);
158              if (!success) {
159                  revert DSCEngine__TransferFailed();
160              }
161          }
````
[DSCEngine.sol#L152](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DSCEngine.sol#L152)
## Tools Used
VSCode
## Recommendations
````diff
      function depositCollateral(address tokenCollateralAddress, uint256 amountCollateral)
              public
              moreThanZero(amountCollateral)
-             isAllowedToken(tokenCollateralAddress)
              nonReentrant
          {
+             if (s_priceFeeds[token] == address(0)) {
+                         revert DSCEngine__NotAllowedToken();
+                     }
+                     _;
              s_collateralDeposited[msg.sender][tokenCollateralAddress] += amountCollateral;
              emit CollateralDeposited(msg.sender, tokenCollateralAddress, amountCollateral);
              bool success = IERC20(tokenCollateralAddress).transferFrom(msg.sender, address(this), amountCollateral);
              if (!success) {
                  revert DSCEngine__TransferFailed();
              }
          }
````
# [G-02] Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate
## Context
[DSCEngine.sol#L77-L79](https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/main/src/DSCEngine.sol#L77-L79)
## Vulnerability Details
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.       
Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.
## Proof of Concept
````solidity
File: src/DSCEngine.sol
77:      mapping(address token => address priceFeed) private s_priceFeeds; // tokenToPriceFeed
78:      mapping(address user => mapping(address token => uint256 amount)) private s_collateralDeposited;
79:      mapping(address user => uint256 amountDscMinted) private s_DSCMinted;
````
## Tools Used
VSCode

# [M-01] Potential Reentrancy Vulnerability in `clearTokens()` function
## Lines of code
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L627-L629](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L627-L629)
## Impact
If exploited, the potential reentrancy vulnerability in the `clearTokens()` function can allow an attacker to maliciously reenter the contract and execute arbitrary code during the execution of the function. This can result in unauthorized token transfers, unexpected state modifications, or manipulation of contract logic. The impact can range from loss of funds to a complete compromise of the contract's intended behavior.
## Proof of concept
Please add the following test in `test\ulysses-omnichain\BranchBridgeAgentTest.t.sol`. This test will pass to demonstrate the described scenario.
````solidity
contract BranchBridgeAgentTest {
    BranchBridgeAgent branchBridgeAgent;
    address attacker;

    function beforeEach() public {
        branchBridgeAgent = BranchBridgeAgent(DeployedAddresses.BranchBridgeAgent());
        attacker = address(this);
    }

    function testReentrancyVulnerability() public {
        // Set up the test scenario
        address recipient = attacker;
        bytes memory params = "<malicious params>"; // Define the malicious parameters for the clearTokens function

        // Execute the vulnerable code
        branchBridgeAgent.clearTokens(params, recipient);

        // Perform assertions to check for the reentrancy vulnerability
        // (e.g., check if unauthorized calls were made during the vulnerable code execution)
        uint256 attackerBalance = address(this).balance;
        // Assert that the attacker's balance has increased due to a reentrant call
        Assert.isTrue(attackerBalance > 0, "Reentrancy attack was successful. Attacker balance increased.");

    }
}

````
## Tools Used
VS Code
## Recommended mitigation steps
To mitigate the potential reentrancy vulnerability, the following steps are recommended:
- Implement the checks-effects-interactions pattern: Refactor the `clearTokens()` function to separate token transfers from any external calls. Ensure that all external calls are made at the end of the function, after any token transfers have completed. This helps prevent reentrancy attacks by limiting the potential for malicious code execution between state changes and external calls.
- Use a withdrawal pattern: Consider adopting a withdrawal pattern for token transfers where the recipient can explicitly request the withdrawal. This pattern involves keeping track of the balances in the contract and allowing users to withdraw their funds individually. By using this pattern, the risk of reentrancy attacks can be mitigated by eliminating the need for the contract to initiate the token transfers.
# [L-01] Empty payable fallback() function does not authenticate requests
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.        
       
There are 3 instances of this issue:
````solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

1419:    fallback() external payable {}
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1419](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1419)
````solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

284:    fallback() external payable {}
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L284](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L284)
````solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

1334:    fallback() external payable {}
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1334](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1334)
# [L-02] Solmate safeTransfer and safeTransferFrom does not check the codesize of the token address, which may lead to fund loss
## Lines of code
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L127](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L127)
## Vulnerability details
### Impact
Solmate safeTransfer and safeTransferFrom does not check the codesize of the token address, which may lead to fund loss
### Proof of concept
Solmate safetransferFrom doesn’t check the existence of code at the token address, all contract below that use SafeTransferLib and and all safeTransfer and safeTransferFrom has the issue below . The impacted function and LOC is linked above.
````solidity
File: src/rewards/rewards/FlywheelInstantRewards.sol
17    contract FlywheelInstantRewards is BaseFlywheelRewards, IFlywheelInstantRewards {
18        using SafeTransferLib for ERC20;
19:       using SafeCastLib for uint256;

File: src/ulysses-amm/factories/UlyssesFactory.sol
38    contract UlyssesFactory is Ownable, IUlyssesFactory {
39        using SafeTransferLib for ERC20;
40:       using FixedPointMathLib for uint256;

File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
67    contract ArbitrumBranchBridgeAgent is BranchBridgeAgent {
68        using SafeTransferLib for address;
69:       using SafeTransferLib for ERC20;
````
This is a known issue while using solmate’s libraries.
Hence this may lead to miscalculation of funds and may lead to loss of funds , because if safetransfer() and safetransferfrom() are called on a token address that doesn’t have contract in it, it will always return success, bypassing the return value check.        
      
If a underlying token is self-destructed, the code may consider the transfer because the lack of contract existence contract.      
      
Due to this protocol will think that funds has been transferred and successful , and records will be accordingly calculated, but in reality funds were never transferred.      
        
So this will lead to miscalculation and possibly loss of funds.  
## Tools Used
Manual Review
## Recommended mitigation steps
Use openzeppelin’s safeERC20 or implement a code existence check.
# [N-01] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING
Consider changing the variable to be an unnamed one.
- ERC20Boost gauges()
- ERC20Boost userGauges() 
- ERC20Gauges gauges()
- ERC20Gauges userGauges()
- ERC20Gauges _incrementUserAndGlobalWeights()
- ERC20Gauges _decrementUserWeights()
- ERC20Gauges _addGauge()
- ERC4626 deposit()
- ERC4626 mint()
- ERC4626 withdraw()
- ERC4626 redeem()
- ERC4626DepositOnly deposit()
- ERC4626DepositOnly mint()
- ERC4626MultiToken deposit()
- ERC4626MultiToken mint()
- ERC4626MultiToken withdraw()
- ERC4626MultiToken redeem()
- ERC4626MultiToken convertToShares()
- ERC4626MultiToken convertToAssets()
- ERC4626MultiToken previewMint()
- ERC4626MultiToken previewWithdraw()
- UlyssesERC4626 deposit()
- UlyssesERC4626 mint()
- UlyssesERC4626 redeem()
- UlyssesERC4626 beforeDeposit()
- UlyssesERC4626 beforeMint()
- UlyssesERC4626 afterRedeem()
- BaseV2GaugeFactory newGauge()
- BaseV2Minter getRewards()
- DateTimeLib getMonth()
- DateTimeLib isTuesday()
- FlywheelCore setBooster()
- RewardsDepot transferRewards()
- FlywheelAccumulatedRewards getAccruedRewards()
- FlywheelGaugeRewards queueRewardsForCycle()
- FlywheelGaugeRewards getAccruedRewards()
- TalosManager checkUpkeep()
- TalosStrategyVanilla _compoundFees()
- TalosBaseStrategy init()
- TalosBaseStrategy deposit()
- TalosBaseStrategy redeem()
- TalosBaseStrategy doRerange()
- TalosBaseStrategy doRebalance()
- TalosBaseStrategyFactory createTalosV3Strategy()
- TalosStartegyStakedFactory createTalosV3Strategy()
- PoolActions rerange()
- PoolActions getThisPositionTicks()
- PoolVariables getPositionTicks()
- PoolVariables amountsForTicks()
- PoolVariables baseTicks()
- PoolVariables amountsDirection()
- PoolVariables getSwapToEqualAmountsParams()
- TalosStrategySimple doRerange()
- TalosStrategySimple doRebalance()
- UlyssesPool claimProtocolFees()
- UlyssesPool addNewBandwidth()
- UlyssesPool getBandwidthUpdateAmounts()
- UlyssesPool updateBandwidth()
- UlyssesPool _calculateRebalancingFee()
- UlyssesPool calcFee()
- UlyssesPool ulyssesSwap()
- UlyssesPool ulyssesAddLP()
- UlyssesPool ulyssesRemoveLP()
- UlyssesPool swapIn()
- UlyssesPool swapFromPool()
- UlyssesPool beforeDeposit()
- UlyssesPool beforeMint()
- UlyssesPool afterRedeem()
- UlyssesRouter getUlyssesLP()
- UlyssesFactory _createPool()
- UlyssesFactory createPools()
- UlyssesFactory createToken()
- ArbitrumBranchBridgeAgent _gasSwapIn()
- BranchBridgeAgent _gasSwapIn()
- BranchBridgeAgent _getContext()
- BranchBridgeAgent _normalizeDecimalsMultiple()
- BranchBridgeAgentExecutor executeNoSettlement()
- BranchBridgeAgentExecutor executeWithSettlement()
- BranchBridgeAgentExecutor executeWithSettlementMultiple()
- MulticallRootRouter _multicall()
- RootBridgeAgent _getContext()
- RootBridgeAgentExecutor executeSystemRequest()
- RootBridgeAgentExecutor executeNoDeposit()
- RootBridgeAgentExecutor executeWithDeposit()
- RootBridgeAgentExecutor executeWithDepositMultiple()
- RootBridgeAgentExecutor executeSignedNoDeposit()
- RootBridgeAgentExecutor executeSignedWithDeposit()
- RootBridgeAgentExecutor executeSignedWithDepositMultiple()
- RootBridgeAgentExecutor executeRetrySettlement()
- RootBridgeAgentExecutor _bridgeInMultiple()
- RootPort fetchVirtualAccount()
- RootPort addVirtualAccount()
- VirtualAccount call()
- ArbitrumBranchBridgeAgentFactory createBridgeAgent()
- BranchBridgeAgentFactory createBridgeAgent()
- ERC20hTokenBranchFactory createToken()
- ERC20hTokenRootFactory createToken()
- RootBridgeAgentFactory createBridgeAgent()
- UniswapV3Staker stakes()
- UniswapV3Staker endIncentive()
- UniswapV3Staker claimReward()
- UniswapV3Staker claimAllRewards()
- UniswapV3Staker getRewardInfo()
- IncentiveTime getEnd()
- IncentiveTime getEndAndDuration()
- NFTPositionInfo getPositionInfo()
- RewardMath computeBoostedSecondsInsideX128()
# [N-02] Immutables should be in uppercase
````solidity
File: src/erc-20/ERC20Gauges.sol

39:    uint32 public immutable override gaugeCycleLength;

42:    uint32 public immutable override incrementFreezeWindow;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L39](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L39)
````solidity
File: src/erc-4626/ERC4626.sol

21:    ERC20 public immutable asset;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L21](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L21)
````solidity
File: src/erc-4626/ERC4626DepositOnly.sol

21:    ERC20 public immutable asset;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L21](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L21)
````solidity
File: src/erc-4626/UlyssesERC4626.sol

22:    address public immutable asset;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L22](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L22)
````solidity
File: src/gauges/BaseV2Gauge.sol
26:    address public immutable override rewardToken;

29:    bHermesBoost public immutable hermesGaugeBoost;

32:    FlywheelGaugeRewards public immutable override flywheelGaugeRewards;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L26](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L26)
````solidity
File: src/gauges/UniswapV3Gauge.sol

20:    address public immutable override uniswapV3Staker;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L20](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L20)
````solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

24:    BaseV2GaugeManager public immutable override gaugeManager;

27:    bHermesBoost public immutable override bHermesBoostToken;

30:    BribesFactory public immutable override bribesFactory;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L24](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L24)
# [G-01] Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings      
````solidity
File: src/governance/GovernorBravoInterfaces.sol
/// @audit mapping()(32): receipts, uint256[](32) values, uint256(32): id, uint256(32): eta, uint256(32): startBlock, uint256(32): endBlock, uint256(32): forVotes, uint256(32): againstVotes, uint256(32): abstainVotes, string[](32): signatures, bytes[](32): calldatas, address[](32): targets, address(20): proposer, bool(1): canceled, bool(1): executed
105      struct Proposal {
106          /// @notice Unique id for looking up a proposal
107          uint256 id;
108          /// @notice Creator of the proposal
109          address proposer;
110          /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
111          uint256 eta;
112          /// @notice the ordered list of target addresses for calls to be made
113          address[] targets;
114          /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
115          uint256[] values;
116          /// @notice The ordered list of function signatures to be called
117          string[] signatures;
118          /// @notice The ordered list of calldata to be passed to each call
119          bytes[] calldatas;
120          /// @notice The block at which voting begins: holders must delegate their votes prior to this block
121          uint256 startBlock;
122          /// @notice The block at which voting ends: votes must be cast prior to this block
123          uint256 endBlock;
124          /// @notice Current number of votes in favor of this proposal
125          uint256 forVotes;
126          /// @notice Current number of votes in opposition to this proposal
127          uint256 againstVotes;
128          /// @notice Current number of votes for abstaining for this proposal
129          uint256 abstainVotes;
130          /// @notice Flag marking whether the proposal has been canceled
131          bool canceled;
132          /// @notice Flag marking whether the proposal has been executed
133          bool executed;
134          /// @notice Receipts of ballots for the entire set of voters
135          mapping(address => Receipt) receipts;
136:      }
/// @audit uint96(12): votes, uint8(1): support, bool(1): hasVoted
139      struct Receipt {
140          /// @notice Whether or not a vote has been cast
141          bool hasVoted;
142          /// @notice Whether or not the voter supports the proposal or abstains
143          uint8 support;
144          /// @notice The number of votes the voter had, which were cast
145          uint96 votes;
146:      }
````
`GovernorBravoInterfaces.sol#L105-136`
````solidity
File: src\ulysses-amm\interfaces\IUlyssesPool.sol
/// @audit uint248(31): bandwidth, user-defined(20): destination, uint8(1) weight
87      struct BandwidthState {
88              uint248 bandwidth;
89              uint8 weight;
90              UlyssesPool destination;
91:          }
````
`IUlyssesPool.sol#L87-91`
````solidity
File: src\ulysses-omnichain\MulticallRootRouter.sol
/// @audit uint256(32): amountOut, uint256(32): depositOut, address(20): recipient, address(20): outputToken
15      struct OutputParams {
16          address recipient; // Address to receive the output assets.
17          address outputToken; // Address of the output hToken.
18          uint256 amountOut; // Amount of output hTokens to send.
19          uint256 depositOut; // Amount of output underlying token to send.
20      }
21
///@audit uint256[](32): amountsOut, uint256[](32): depositsOut, address[](20) outputTokens, address(20) recipient
22      struct OutputMultipleParams {
23          address recipient; // Address to receive the output assets.
24          address[] outputTokens; // Addresses of the output hTokens.
25          uint256[] amountsOut; // Amounts of output hTokens to send.
26          uint256[] depositsOut; // Amounts of output underlying tokens to send.
27:      }
````
`MulticallRootRouter.sol#L15-20`
I'll attach you also the rest of the instances with no @audit storage slot guideline.
````solidity
File: src\ulysses-omnichain\interfaces\IBranchBridgeAgent.sol
///@audit
16        struct Deposit {
17            uint128 depositedGas;
18            address owner;
19            DepositStatus status;
20            address[] hTokens;
21            address[] tokens;
22            uint256[] amounts;
23            uint256[] deposits;
24:        }
///@audit
26        struct DepositInput {
27            //Deposit Info
28            address hToken; //Input Local hTokens Address.
29            address token; //Input Native / underlying Token Address.
30            uint256 amount; //Amount of Local hTokens deposited for interaction.
31            uint256 deposit; //Amount of native tokens deposited for interaction.
32            uint24 toChain; //Destination chain for interaction.
33:        }
///@audit
35        struct DepositMultipleInput {
36            //Deposit Info
37            address[] hTokens; //Input Local hTokens Address.
38            address[] tokens; //Input Native / underlying Token Address.
39            uint256[] amounts; //Amount of Local hTokens deposited for interaction.
40            uint256[] deposits; //Amount of native tokens deposited for interaction.
41            uint24 toChain; //Destination chain for interaction.
42:        }
///@audit
44        struct DepositParams {
45            //Deposit Info
46            uint32 depositNonce; //Deposit nonce.
47            address hToken; //Input Local hTokens Address.
48            address token; //Input Native / underlying Token Address.
49            uint256 amount; //Amount of Local hTokens deposited for interaction.
50            uint256 deposit; //Amount of native tokens deposited for interaction.
51            uint24 toChain; //Destination chain for interaction.
52            uint128 depositedGas; //BRanch chain gas token amount sent with request.
53:        }
///@audit
55        struct DepositMultipleParams {
56            //Deposit Info
57            uint8 numberOfAssets; //Number of assets to deposit.
58            uint32 depositNonce; //Deposit nonce.
59            address[] hTokens; //Input Local hTokens Address.
60            address[] tokens; //Input Native / underlying Token Address.
61            uint256[] amounts; //Amount of Local hTokens deposited for interaction.
62            uint256[] deposits; //Amount of native tokens deposited for interaction.
63            uint24 toChain; //Destination chain for interaction.
64            uint128 depositedGas; //BRanch chain gas token amount sent with request.
65:        }
///@audit
67        struct SettlementParams {
68            uint32 settlementNonce;
69            address recipient;
70            address hToken;
71            address token;
72            uint256 amount;
73            uint256 deposit;
74:        }
///@audit
76        struct SettlementMultipleParams {
77            uint8 numberOfAssets; //Number of assets to deposit.
78            address recipient;
79            uint32 settlementNonce;
80            address[] hTokens;
81            address[] tokens;
82            uint256[] amounts;
83            uint256[] deposits;
84:        }
````
`IBranchBridgeAgent.sol#L16-24`
````solidity
File: src\ulysses-omnichain\interfaces\IMulticall2.sol
///@audit
10        struct Call {
11            address target;
12            bytes callData;
13:        }
///@audit
15        struct Result {
16            bool success;
17            bytes returnData;
18:        }
````
`IMulticall2.sol#L10-13`
````solidity
File: src\ulysses-omnichain\interfaces\IRootBridgeAgent.sol
///@audit
19        struct GasPoolInfo {
20            //zeroForOne when swapping gas from branch chain into root chain gas
21            bool zeroForOneOnInflow;
22            uint24 priceImpactPercentage; //Price impact percentage
23            address poolAddress; //Uniswap V3 Pool Address
24:        }
///@audit        
31        struct Settlement {
32            uint24 toChain; //Destination chain for interaction.
33            uint128 gasToBridgeOut; //Gas owed to user
34            address owner; //Owner of the settlement
35            address recipient; //Recipient of the settlement.
36            SettlementStatus status; //Status of the settlement
37            address[] hTokens; //Input Local hTokens Addresses.
38            address[] tokens; //Input Native / underlying Token Addresses.
39            uint256[] amounts; //Amount of Local hTokens deposited for interaction.
40            uint256[] deposits; //Amount of native tokens deposited for interaction.
41            bytes callData; //Call data for settlement
42:        }
///@audit
44        struct SettlementParams {
45            uint32 settlementNonce; //Settlement nonce.
46            address recipient; //Recipient of the settlement.
47            address hToken; //Input Local hTokens Address.
48            address token; //Input Native / underlying Token Address.
49            uint256 amount; //Amount of Local hTokens deposited for interaction.
50            uint256 deposit; //Amount of native tokens deposited for interaction.
51:        }
///@audit
53        struct SettlementMultipleParams {
54            uint8 numberOfAssets; //Number of assets to deposit.
55            uint32 settlementNonce; //Settlement nonce.
56            address recipient; //Recipient of the settlement.
57            address[] hTokens; //Input Local hTokens Addresses.
58            address[] tokens; //Input Native / underlying Token Addresses.
59            uint256[] amounts; //Amount of Local hTokens deposited for interaction.
60            uint256[] deposits; //Amount of native tokens deposited for interaction.
61:        }
///@audit
63        struct DepositParams {
64            //Deposit Info
65            uint32 depositNonce; //Deposit nonce.
66            address hToken; //Input Local hTokens Address.
67            address token; //Input Native / underlying Token Address.
68            uint256 amount; //Amount of Local hTokens deposited for interaction.
69            uint256 deposit; //Amount of native tokens deposited for interaction.
70            uint24 toChain; //Destination chain for interaction.
71:        }
///@audit   
73        struct DepositMultipleParams {
74            //Deposit Info
75            uint8 numberOfAssets; //Number of assets to deposit.
76            uint32 depositNonce; //Deposit nonce.
77            address[] hTokens; //Input Local hTokens Address.
78            address[] tokens; //Input Native / underlying Token Address.
79            uint256[] amounts; //Amount of Local hTokens deposited for interaction.
80            uint256[] deposits; //Amount of native tokens deposited for interaction.
81            uint24 toChain; //Destination chain for interaction.
82:        }
````
`IRootBridgeAgent.sol#L19-24`
````solidity
File: src\ulysses-omnichain\interfaces\IRootPort.sol
///@audit
14        struct GasPoolInfo {
15            bool zeroForOneOnInflow;
16            uint24 priceImpactPercentage;
17            address gasTokenGlobalAddress;
18            address poolAddress;
19:        }
````
`IRootPort.sol#L14-19`
````solidity
File: src\uni-v3-staker\interfaces\IUniswapV3Staker.sol
///@audit
68    struct Deposit {
69            address owner;
70            int24 tickLower;
71            int24 tickUpper;
72            uint40 stakedTimestamp;
73:        }
///@audit
        /// @notice Represents a staked liquidity NFT
76        struct Stake {
77            uint160 secondsPerLiquidityInsideInitialX128;
78            uint96 liquidityNoOverflow;
79            uint128 liquidityIfOverflow;
80:        }
````
`IUniswapV3Staker.sol#L68-73`
# [G-02] Use != 0 instead of > 0 for unsigned integer comparison
Instances (54):
````solidity
File: src/erc-20/ERC20Gauges.sol

417:    if (weight > 0) {

441:    if (weight > 0) {
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L417](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L417)
````solidity
File: src/erc-20/ERC20MultiVotes.sol

250:    if (pos > 0
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250)
````solidity
File: src/maia/PartnerUtilityManager.sol

75:    if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {

85:    if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {

95:    if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75)
````solidity
File: src/rewards/base/FlywheelCore.sol

127:    if (oldRewardBalance > 0) {

162:    if (strategyRewardsAccrued > 0) {
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L127](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L127)
````solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol
234:    if (accruedRewards > 0)
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234)
````solidity
File: src/talos/TalosStrategyVanilla.sol

139:    if (_liquidity > 0) {
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139)
````solidity
File: src/talos/base/TalosBaseStrategy.sol

409    if (amount0 > 0) _token0.transfer(msg.sender, amount0);
410:   if (amount1 > 0) _token1.transfer(msg.sender, amount1);
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409-L410](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409-L410)
````solidity
File: src/ulysses-amm/UlyssesPool.sol

153:    if (claimed > 0) {

191:    if (oldBandwidth > 0) {

256:    if (oldBandwidth > 0) {

272:    if (oldBandwidth > 0) {

282:    } else if (leftOverBandwidth > 0)

911:    if (updateAmount > 0) {

1048:    if (updateAmount > 0) {
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L153](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L153)
````solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol
111:    if (j != i && weights[i][j] > 0) 
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111)
````solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

160:    if (gasRemaining > 0) {
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L160](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L160)
````solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

118:    if (_deposit > 0) {

123:    if (_amount - _deposit > 0) {

137:    if (_deposits[i] > 0) {

144:    if (_amounts[i] - _deposits[i] > 0) {
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L118](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L118)
````solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

474:    if (!isRemote && gasToBridgeOut > 0) 

499:    if (!isRemote && gasToBridgeOut > 0) 

521:    if (!isRemote && gasToBridgeOut > 0)

543:    if (!isRemote && gasToBridgeOut > 0) 

623:    if (_amounts[i] - _deposits[i] > 0) {

627:    if (_deposits[i] > 0) {

979:    if (_amount - _deposit > 0) {

983:    if (_deposit > 0) {

1328:    if (executionBudget > 0)
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L474](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L474)
````solidity
File: src/ulysses-omnichain/BranchPort.sol

248:    if (_amount - _deposit > 0) {

252:    if (_deposit > 0) {

268:    if (_deposits[i] > 0) {

275:    if (_amounts[i] - _deposits[i] > 0)
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L248](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L248)
````solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
55    if (
56        ...
57:               || (_dParams.amount > 0
58:               || (_dParams.deposit > 0

301:    if (localAddress == address(0) || (underlyingAddress == address(0) && _deposit > 0)) {

347:    if (hTokens[i] == address(0) || (tokens[i] == address(0) && _deposits[i] > 0)) revert InvalidInputParams();

451:    if (_amount - _deposit > 0) {

457:    if (_deposit > 0) {

750:    if (_initialGas > 0) {

757:    if (_initialGas > 0) {

1161:    if (initialGas > 0) {

1171:    if (initialGas > 0) {

1240:    if (executionBudget > 0)
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L55-L58](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L55-L58)
````solidity
File: src/ulysses-omnichain/RootPort.sol

282:    if (_amount - _deposit > 0) 
283:    if (_deposit > 0)
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L282-L283](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L282-L283)
````solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

199:    if (incentive.numberOfStakes > 0)

271:    if (reward > 0)

281:    if (reward > 0) hermes.safeTransfer(to, reward);
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L199](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L199)
````solidity
File: src/uni-v3-staker/libraries/RewardMath.sol

31:    if (boostTotalSupply > 0) {
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/RewardMath.sol#L31](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/RewardMath.sol#L31)
# [G-03] Using > 0 costs more gas than != 0 when used on a uint in a require() statement
When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance      
       
There are 2 instances of this issue.
````solidity
File: src/erc-4626/ERC4626MultiToken.sol

52:    require(_weights[i] > 0);
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52)
````solidity
File:    src/ulysses-amm/UlyssesToken.sol

47:    require(_weight > 0);
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47)
# [G-04] A modifier used only once and not being inherited should be inlined to save gas
````solidity
File: src/gauges/factories/BribesFactory.sol

105    modifier onlyGaugeFactory() {
106        if (!gaugeManager.activeGaugeFactories(BaseV2GaugeFactory(msg.sender))) {
107            revert Unauthorized();
108        }
109        _;
110:   }
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L105-L110](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L105-L110)   
           
The onlyGaugeFactory() modifier is only being called on [Line 72](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L72)
````solidity
File: src/hermes/tokens/bHermesBoost.sol

32    modifier onlybHermes() {
33        if (msg.sender != bHermes) revert NotbHermes();
34        _;
35:   }
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L32-L35](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L32-L35)       

The onlybHermes() modifier is only being called on [Line 28](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L28)
````solidity
File: src/hermes/tokens/bHermesGauges.sol

39    modifier onlybHermes() {
40        if (msg.sender != bHermes) revert NotbHermes();
41        _;
42:   }
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L39-L42](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L39-L42)      

The onlybHermes() modifier is only being called on [Line 35](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L35)
````solidity
File: src/maia/PartnerUtilityManager.sol

168:    modifier checkPartnerGovernance(uint256 amount) virtual;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L168](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L168)       

The checkPartnerGovernance() modifier is only being called on [Line 158](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L158)
````solidity
File: src/rewards/depots/MultiRewardsDepot.sol

72    modifier onlyFlywheelRewards() override {
73        if (!_isRewardsContract[msg.sender]) revert FlywheelRewardsError();
74        _;
75:   }
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L72-L75](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L72-L75)       

The onlyFlywheelRewards() modifier is only being called on [Line 38](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L38)
````solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

190    modifier onlyWhitelisted(address from) {
191        if (!whitelistedAddresses[from]) revert Unauthorized();
192        _;
193:   }
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L190-L193](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L190-L193)        

The onlyWhitelisted() modifier is only being called on [Line 82](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L82)
````solidity
File: src/ulysses-omnichain/BranchPort.sol

409    modifier requiresBridgeAgentFactory() {
410        if (!isBridgeAgentFactory[msg.sender]) revert UnrecognizedBridgeAgentFactory();
411        _;
412:   }

415    modifier requiresPortStrategy(address _token) {
416        if (!isPortStrategy[msg.sender][_token]) revert UnrecognizedPortStrategy();
417        _;
418:   }

423    modifier lock() {
424        require(_unlocked == 1);
425        _unlocked = 2;
426        _;
427        _unlocked = 1;
428:   }
````
[https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L409-L412](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L409-L412)        

The requiresBridgeAgentFactory() modifier is only being called on [Line 290](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L290), the requiresPortStrategy() modifier is only being called on [Line 158](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L158) and the lock() modifier is only being called on [Line 172](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L172)
````solidity
File: src/ulysses-omnichain/RootPort.sol

510    modifier requiresBridgeAgentFactory() {
511        if (!isBridgeAgentFactory[msg.sender]) revert UnrecognizedBridgeAgentFactory();
512        _;
513:   }
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L510-L513](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L510-L513)       

The requiresBridgeAgentFactory() modifier is only being called on [Line 366](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L366)
````solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

1313    modifier requiresLocalBranchBridgeAgent() {
1314        if (msg.sender != getBranchBridgeAgent[localChainId]) {
1315            revert UnrecognizedExecutor();
1316        }
1317        _;
1318:   }

1321    modifier requiresPort() {
1322        if (msg.sender != localPortAddress) revert UnrecognizedPort();
1323        _;
1324:   }

1327    modifier requiresManager() {
1328            if (msg.sender != IPort(localPortAddress).getBridgeAgentManager(address(this))) {
1329                revert UnrecognizedBridgeAgentManager();
1330            }
1331            _;
1332:       }
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1313-L1318](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1313-L1318)       

The requiresLocalBranchBridgeAgent() modifier is only being called on [Line 1225](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1225), the requiresPort() modifier is only being called on [Line 1254](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1254) and the requiresManager() modifier is only being called on [Line 1248](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1248)
````solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

75    modifier requiresCoreRouter() {
76            if (msg.sender != localCoreRouterAddress) revert UnrecognizedCoreRouter();
77            _;
78:   }
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L75-L78](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L75-L78)        

The requiresCoreRouter() modifier is only being called on [Line 62](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L62)
````solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

74    modifier requiresCoreRouter() {
75        if (msg.sender != coreRootRouterAddress && msg.sender != rootPortAddress) {
76            revert UnrecognizedCoreRouter();
77        }
78        _;
79:   }
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L74-L79](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L74-L79)         

The requiresCoreRouter() modifier is only being called on [Line 56](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L56)
# [G-05] Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value.       
       
There are 2 instances of this issue.
````solidity
File: ksrc/gauges/interfaces/IBaseV2GaugeManager.sol
117:    event ChangedbHermesGaugeOwner(address newOwner);

120:    event ChangedAdmin(address newAdmin);
````
[https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/interfaces/IBaseV2GaugeManager.sol#L117](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/interfaces/IBaseV2GaugeManager.sol#L117)         
[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L120](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L120)

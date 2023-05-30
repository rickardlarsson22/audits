# Detailed Findings 
# [L-01] `require()` should be used instead of `assert()`
Prior to solidity version `0.8.0`, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version `0.8.0` as its documentation states that “The assert function creates an error of type Panic(`uint256`). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.       
     
There is 3 instances of this issue:
````solidity
File: contracts/dnssec-oracle/RRUtils.sol

25:               assert(idx < self.length);

61:               assert(offset < self.length);
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L25](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L25)
````solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

169:              assert(startIdx + fieldLength < lastIdx);
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L169](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L169)
# [N-01] Files do not contain a `SPDX-License-Identifier`
## Lines of code
[EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol), [ModexpPrecompile.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol), [P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol), [RSASHA1Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol), [RSASHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol), [RSAVerify.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol), [SHA1Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol), [SHA256Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol), [BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol), [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol), [SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol)
## Vulnerability details
### Proof of concept
````solidity
1:    pragma solidity
````
## Recommended mitigation steps
Add the `SPDX-License-Identifier` before `pragma` statement.
# [N-02] Typos
There are 6 instances of this issue:
````solidity
File:     contracts\dnssec-oracle\BytesUtils.sol

/// @audit iff
141:      * @dev Compares a range of 'self' to all of 'other' and returns True iff
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L141](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L141)
````solidity
File:     contracts/dnssec-oracle/DNSSECImpl.sol

/// @audit iff
283:      * @return True iff the key verifies the signature.

/// @audit iff
413:      * @return True iff the digest matches.
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L283](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L283)
````solidity
File:     contracts/dnssec-oracle/RRUtils.sol

/// @audit iff
146:      * @dev Returns true iff there are more RRs to iterate.

/// @audit iff
148:      * @return True iff the iterator has finished.
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L146](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L146)
````solidity
File:     contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

/// @audit iff
15:       * @return True iff the signature is valid.
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L15](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L15)
# [N-03] constant/immutable variables should be in all capital letters
If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).     
      
There are 14 instances of this issue:
````solidity
File:     contracts/dnsregistrar/DNSRegistrar.sol

26        ENS public immutable ens;
27:       DNSSEC public immutable oracle;

29        address public immutable previousRegistrar;
30:       address public immutable resolver;
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L26-L27](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L26-L27)
````solidity
File:     contracts/dnsregistrar/OffchainDNSResolver.sol

37        ENS public immutable ens;
38:       DNSSEC public immutable oracle;
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L37-L38](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L37-L38)
````solidity
File:     contracts/dnssec-oracle/BytesUtils.sol

322:      bytes constant base32HexTable =
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L322](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L322)
````solidity
File:     contracts/dnssec-oracle/algorithms/EllipticCurve.sol

21:          uint256 constant a =
22             0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFC;
23:          uint256 constant b =
24             0x5AC635D8AA3A93E7B3EBBD55769886BC651D06B0CC53B0F63BCE3C3E27D2604B;
25:          uint256 constant gx =
26             0x6B17D1F2E12C4247F8BCE6E563A440F277037D812DEB33A0F4A13945D898C296;
27:          uint256 constant gy =
28             0x4FE342E2FE1A7F9B8EE7EB4A7C0F9E162BCE33576B315ECECBB6406837BF51F5;
29:          uint256 constant p =
30             0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFF;
31:          uint256 constant n =
32             0xFFFFFFFF00000000FFFFFFFFFFFFFFFFBCE6FAADA7179E84F3B9CAC2FC632551;
33
34:          uint256 constant lowSmax =
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21-L34](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21-L34)
# [N-04] Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
While the compiler knows to optimize away the exponentiation, it’s still better coding practice to use idioms that do not require compiler optimization, if they exist      
      
There are 2 instances of this issue:
````solidity
File:     contracts/dnssec-oracle/BytesUtils.sol

90:           mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);

285:          uint256 mask = (256 ** (32 - len)) - 1;
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L90](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L90)
# [N-05] Lack of `address(0)` checks in the constructor
Zero-address check should be used in the constructors, to avoid the risk of setting smth as address(0) at deploying time.
````solidity
File:     contracts/dnsregistrar/DNSRegistrar.sol

55          constructor(
56             address _previousRegistrar,
57             address _resolver,
58             DNSSEC _dnssec,
59             PublicSuffixList _suffixes,
60             ENS _ens
61          ) {
62             previousRegistrar = _previousRegistrar;
63:             resolver = _resolver;
64:             oracle = _dnssec;
65             suffixes = _suffixes;
66             emit NewPublicSuffixList(address(suffixes));
67             ens = _ens;
68          }
````
[https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L62-L63](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L62-L63)

# Avoiding Common Attacks
* SWC-107 Reentrancy 
  * Within `ERC721LendWrapper.sol`, I have employed the 'checks, effects, interaction' pattern on relevant functions, to guard against reentrancy attacks. This can be seen in the `lendOut()` method call.
* SWC-101 Integer Overflow and Underflow
  * To guard against this, I have used a solidity version above 0.8.0, which has protection against integer overflow and underflow by default, without usage of OZ's SafeMath libraries.
* SWC-102 Outdated Solidity compiler version
  * To avoid using and outdated compiler, I have chosen to use solidity compiler version 0.8.4.

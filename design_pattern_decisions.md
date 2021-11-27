# Design Pattern Decisions
1. **Inter-Contract Execution** - `Lender.sol` deploys `ERC721LendWrapper.sol` and performs contract calls on it, in order to provide a unified API for lending and checking the virtual owner of NFTs.
2. **Inheritance and Interfaces** -  `ERC721LendWrapper.xol` inherit/implements the following contracts and interfaces from OZ's libraries: 
  * `ERC721` - A lendWrapper token is an NFT that can be traded
  * `ERC721Burnable` - A lendWrapper token can be burnt to release the wrapped NFT
  * `IERC721Receiver` - A lendWrapper token can receive ERC-721 compliant NFTs to wrap them. I also added a check here to reject any non-supported token addresses.

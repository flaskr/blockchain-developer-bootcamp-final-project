# Proof of NFT
The application will provide let a digital or real life party verify that a particular person they are communicating with does indeed own an NFT. + NFT rental

## NFT ownership verification
- Party A can issue a challenge for an NFT holder
  - Presented as a QR code or link
  - Challenge data:
    - one-time code unique to challenge instance
    - request start-time (may be way before now to simulate a 'snapshot')
    - request expiry
    - token address of NFT ??
    - challenge response endpoint
- Party B can scan the QR code to obtain the signing one-time-code along with forwarding address
- client app can potentially do a lookup onchain to see if their account owns the NFT/collection, and refuse to proceed. This is not the final verification, though.
- Party B's app will sign the challenge using the private key of their account, and forward the signature to the response endpoint
- nodejs on endpoint receives response - verify that 
  - sender of message is from an address that owns the nft
  - sender has owned the NFT from 'request start-time' to now
  - We have not exceeded request expiry
## NFT rental
Party A is putting up their NFT X for rent. Party B is trying to rent the NFT for a given amount of time.
- Party A can put NFT X in Rental.sol and receive NFT Y in exchange
- Party B can pay Ether to receive NFT Z as a representation of holding 
  - NFT Z becomes 'invalid' after a certain period.
  - NFT Z becomes 'valid' for another user pays to own it
- Rental.sol exposes public methods that can let us verify which address owns NFT X (really Z) for a given period



# Blockchain Developer Bootcamp Final Project - NFT Lend
## URL of deployed application
http://faulty-hill.surge.sh/

You will need some ERC-721 in your account to test this. 

## Running the project locally
### Prerequisites
* [yarn](https://classic.yarnpkg.com/lang/en/docs/install/) should be installed
* [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) should be installed
* Node.js >= v14
* Browser with Metamask extension installed

### Contracts
1. `yarn install` to install dependencies
2. `yarn compile` to compile smart contracts
3. `yarn test` to run unit tests

### Deploy contracts to local network, and setup test environment
4. Start a new terminal, and run `yarn hardhat node`
   1. We will make use of the generated account's private keys for testing, so please take note of:
      1. Account 1: Lender of NFT, deployer of contracts
      2. Account 2: Borrower of NFT
5. A local hardhat node should be running, and you'll see: `Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/`
6. Leave the terminal running until the end of tests, and use another one for the subsequent steps
7. `yarn hardhat run scripts/testing/deploy_w_testtokens.ts --network localhost` to 
   1. deploy test NFT to local hardhat node
      1. Take note of NFT address printed in console
   2. mint 3 NFTs to account 1
      1. Take note that minted NFTs have token-ids `0`, `1`, and `2`
   3. deploy the lender contract.
      1. Take note of the `Lender` contract

### Start UI locally
8. Use the lender contract address from step 7.3.1 and update the following variables in `frontend/src/App.svelte`
   ```
   const lenderAddress = '<>'; // address from step 7.3.1
   const contractCreationBlock = 0; // set this to 0
   ```
9. `cd frontend`
10. `npm install` to install frontend dependencies
11. `npm run build` to build the frontend
12. `npm run dev` to start the frontend, it should be deployed to http://localhost:5000

## Screencast link

https://youtu.be/fsz7ex5a-JY

## Public Ethereum wallet for certification:

`0x8C67b4AAF78CF53b624d8bcB63Ca7513A5634203`

## Project description

### NFT Lend
The application will provide an interface that allows a user (user A) to lend out an NFT to another user (user B) for set period of time (eg. 1 day). The borrower (user B) can freely transfer the temporary ownership (represented by a wrapper NFT).
The application will also provide an interface for anyone to check the current owner of a lent NFT.
A borrower should be able to return the borrowed NFT by burning the wrapper NFT.

## Workflows
### Simple lending workflow
#### Lender
1. Enter website
2. Login with Metamask
3. Enter NFT Token address, token id, and borrower address
4. Click 'Lend'
5. Use the 'verify ownership' form to check that the contract is returning the borrower's address as the virtual owner.

#### Borrower
1. Enter website
2. Connect with Metamask
3. Observe that the borrowed NFT is showing up under 'borrowed' section.

### Collecting an NFT that was borrowed (Not implemented in UI)
#### Lender
1. After the lending duration has expired, enter the website
2. Connect with Metamask
3. Click on 'Collect' on an NFT under the 'lent' section
4. Use the 'verify ownership' to check that you are the new virtual owner. NFT contract should also say that you are the current owner.

### Returning an NFT early (Not implemented in UI)
#### Borrower
1. Enter the website
2. Connect with Metamask
3. Click on 'Return' to burn the wrapped token and return the lent token to the original owner.

## Directory structure

- `frontend`: Project's React frontend.
- `contracts`: Smart contracts that are deployed in the Ropsten testnet.
- `test`: Tests for smart contracts.

## Environment variables (not needed for running project locally)
This is only required for deployment
* Make a copy of `env.example` and name it `env`. Update it with the below values:
* Etherscan_api_key is not required.
* You need an alchemy account or some other ropsten endpoint
* Private key will be the private key of the account that you'll be using for deployment
```
ETHERSCAN_API_KEY=
ROPSTEN_URL=https://eth-ropsten.alchemyapi.io/v2/<YOUR ALCHEMY KEY>
PRIVATE_KEY=
```

## TODO features
- UI for returning of NFT
- UI to display status of lent/borrowed NFTs, and to enable filtering
- Use ERC-1155 to track lending instead
- Support for lending out ERC-1155 tokens


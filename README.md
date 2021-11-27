# Blockchain Developer Bootcamp Final Project - NFT Lend
## Deployed version url:
http://faulty-hill.surge.sh/

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

### Testing on the localhost UI
You can follow these steps to test the smart contract functionality locally if you wish
13. Open your browser, and add the localhost hardhat node to your Metamask as a network with the following parameters:
    - Network Name: `Local Hardhat Network` (naming not important)
    - RPC URL: `http://localhost:8545` (this should match the url in step 5 [start local hardhat node])
    - ChainId: `31337`
    - Currency: `ETH`
14. Add private keys of accounts to MetaMask
    1. 'Account 1': From step 4.1.1
    2. 'Account 2': From step 4.1.2
15. In the browser, navigate to local instance of UI, it should be http://localhost:5000 or the URL provided by console in step 11.
16. Metamask should prompt for connection, if not, click on metamask to connect both Account 1 and Account 2.
17. Make sure you're using Account 1 in Metamask as it has the NFTs.
18. In the first form to 'Lend NFT', enter
    1. NFT token address: from step 7.1.1
    2. Token id: `0` (`1` and `2` are also possible if you wish to repeat this step)
    3. Borrower: Public address of Account 2
19. Click 'Lend' and sign the transaction in Metamask
20. You should see an Alert in browser confirming that address was updated.
21. UI should reflect token `0` under the 'Lent NFTs' section.
22. Using the 'verify ownership' form, and inputting the following should give you Account 2's address:
    1. Token address to check: Test NFT token address
    2. Token id: `0`
23. In Metamask, switch user to Account 2
24. UI should reflect token `0` under 'Borrowed' NFTs section.

## Screencast link



## Public Ethereum wallet for certification:

``

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
3. Observe that 

### Collecting an NFT that was borrowed (Not implemented in UI)
#### Lender
1. After the lending duration has expired, enter the website
2. Connect with Metamask
3. Click on 'Collect' on an NFT under the 'borrowed' section
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


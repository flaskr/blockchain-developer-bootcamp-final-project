# Front-end for NFT Lend

Right now, this is configured to connect to the deployed lender contract in Ropsten test network, as well as to use a default test token contract provided by paradigm's faucet.

## Get started

Install the dependencies...

```bash
npm install
```

To create an optimised version of the app:

```bash
npm run build
```

You can run the newly built app with `npm run start`. 

## Deploying to the web

### With [surge](https://surge.sh/)

Install `surge` if you haven't already:

```bash
npm install -g surge
```

Then, from within your project folder:

```bash
npm run build
surge public <my-project>.surge.sh
```

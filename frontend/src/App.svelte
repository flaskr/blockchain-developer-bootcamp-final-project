<script lang="ts" type="module">
    import { ethers } from './lib/ethers-5.2.esm.min.js';
    import { erc721Lender, erc721LendWrapper, ierc721 } from './abi/imported_abi';

    let provider;
    let signer;


    // 0xf5de760f2e916647fd766b4ad9e85ff943ce3a2b token address | tokens 2221 - 2225
    // 0x895f7eD0CDB2aeE3DD6586617819Bf697c7b5B9A first account
    // 0x85b882d836674de2BE2cBe32386fcd062dC754C7 second account

    const lenderAddress = '0x1e83B1EB6C549353bdc9659737fEc6Ac5Fc500c0'; // ropsten
    const contractCreationBlock = 11468813;

    let lenderContract;

    let lentHistory = [];

    let lentNfts = [];
    let borrowedNfts = [];
    let tokenOwnerDisp = 'none';

    // For the lending form
    let nftToLend = '0xf5de760f2e916647fd766b4ad9e85ff943ce3a2b';
    let tokenIdToLend = '';
    let addressToLend = '';

    // For the verification form
    let lentCheckNft = '0xf5de760f2e916647fd766b4ad9e85ff943ce3a2b';
    let lentCheckTokenId = '';

   // ----- Utility functions ------ /
    async function getLentHistoryForUser() {
        const LENT_ABI = [ "event Lent(address indexed originalTokenAddress, address wrappedTokenAddress, uint256 tokenId, address indexed lender, address indexed borrower, uint256 startTime, uint256 durationInSeconds)" ];

        const contract = new ethers.Contract(lenderAddress, LENT_ABI, provider);
        const signerAddress = await signer.getAddress();
        let lentFilter = contract.filters.Lent(null, null, null, signerAddress);
        lentFilter.fromBlock = contractCreationBlock;
        lentFilter.toBlock = 'latest';
        console.log(lentFilter);

        const lentLogs = await provider.getLogs(lentFilter);
        const lentInterface = new ethers.utils.Interface(erc721Lender.abi);

        return lentLogs.map(log => {
            const parsedLog = lentInterface.parseLog(log).args;
            return {
                address: parsedLog.wrappedTokenAddress,
                borrower: parsedLog.borrower,
                tokenId: parsedLog.tokenId.toNumber(),
                hasPastExpiry: false,
                expiryDate: new Date(parsedLog.startTime.add(parsedLog.durationInSeconds).toNumber() * 1000).toLocaleString()
            }
        });
    }
    async function getBorrowHistoryForUser() {
        const BORROW_ABI = [ "event Lent(address indexed originalTokenAddress, address wrappedTokenAddress, uint256 tokenId, address indexed lender, address indexed borrower, uint256 startTime, uint256 durationInSeconds)" ];

        const contract = new ethers.Contract(lenderAddress, BORROW_ABI, provider);
        const signerAddress = await signer.getAddress();
        let borrowFilter = contract.filters.Lent(null, null, null, null, signerAddress);
        borrowFilter.fromBlock = contractCreationBlock;
        borrowFilter.toBlock = 'latest';
        console.log(borrowFilter);

        const borrowedLogs = await provider.getLogs(borrowFilter);
        const lentInterface = new ethers.utils.Interface(erc721Lender.abi);

        return borrowedLogs.map(log => {
            const parsedLog = lentInterface.parseLog(log).args;
            return {
                address: parsedLog.wrappedTokenAddress,
                tokenId: parsedLog.tokenId.toNumber(),
                hasPastExpiry: false,
                expiryDate: new Date(parsedLog.startTime.add(parsedLog.durationInSeconds).toNumber() * 1000).toLocaleString()
            }
        });
    }

    // ----- User Functions ----- /
    async function approveToken() {

        const nftContract = new ethers.Contract(nftToLend, ierc721.abi, provider);

        nftContract.connect(signer).approve(
            lenderAddress,
            tokenIdToLend
        ).then((e) => {
            alert('Sent approval for NFT!');
            console.log(e);
        })
        .catch((e) => {
            console.log(e);
            console.log('Encountered error while loading data.');
        });
    }

    async function lendOutNft() {
        const secondsNow = Math.floor(Date.now() / 1000);
        lenderContract.connect(signer).lendOut(
            nftToLend,
            tokenIdToLend,
            addressToLend,
            secondsNow,
            86400 // 1 day
        ).then((e) => {
            alert('Lent out NFT!');
            console.log(e);
        })
            .catch((e) => {
                console.log(e);
                console.log('Encountered error while loading data.');
            });
    }

    async function checkOwner() {
        tokenOwnerDisp = await lenderContract.virtualOwnerOf(lentCheckNft, lentCheckTokenId);
    }

    // Load all data for display
    async function loadData() {
        provider = new ethers.providers.Web3Provider(window.ethereum);
        await provider.send("eth_requestAccounts", []);
        signer = provider.getSigner();
        const signerAddress = await signer.getAddress();

        lenderContract = new ethers.Contract(lenderAddress, erc721Lender.abi, provider);

        lentNfts = await getLentHistoryForUser();
        borrowedNfts = await getBorrowHistoryForUser();
    }

    let metamaskIsInstalled: Boolean = typeof window.ethereum !== 'undefined';
    console.log(metamaskIsInstalled);
    if (metamaskIsInstalled) {
        loadData()
            .then(() => console.log('Loaded data!'))
            .catch((e) => {
                console.log(e);
                console.log('Encountered error while loading data.');
            });
    } else {
        console.log('Could not detect Metamask. Please install Metamask before using this application.')
    }


</script>


<body class="container">
    <div class="flex flex-row">
        <div class="flex flex-col p-2 text-center text-xl font-bold">NFT Lend</div>
    </div>
    <div class="flex flex-row p-3">
        <div class="flex flex-col">
            <p class="text-lg text-black font-semibold" >Lend out an NFT</p>
            <div class="flex">
                <div class="flex flex-col bg-gray-100 m-1 p-3 rounded">
                    <label class="p-0.5" for="lend-address">Token Address to lend out:</label>
                    <input class="p-0.5" bind:value={nftToLend} type="text" id="lend-address" name="lend-address">
                    <label class="p-0.5" for="lend-tokenId">TokenId to lend out:</label>
                    <input class="p-0.5" bind:value={tokenIdToLend} type="text" id="lend-tokenId" name="lend-tokenId">
                    <label class="p-0.5" for="lend-to">Address to lend it to:</label>
                    <input class="p-0.5" bind:value={addressToLend} type="text" id="lend-to" name="lend-to">
                    <button class="bg-green-400 text-white text-lg m-2 p-2.5 rounded-lg" on:click={approveToken} >Approve</button>
                    <button class="bg-green-400 text-white text-lg m-2 p-2.5 rounded-lg" on:click={lendOutNft} >Lend Out</button>
                </div>
            </div>
        </div>
        <div class="flex flex-col">
            <p class="text-lg text-black font-semibold" >Verify NFT owner</p>
            <div class="flex">
                <div class="flex flex-col bg-gray-100 m-1 p-3 rounded">
                    <label class="p-0.5" for="lent-check-token">Token Address to check:</label>
                    <input class="p-0.5" bind:value={lentCheckNft} type="text" id="lent-check-token" name="lent-check-token">
                    <label class="p-0.5" for="lent-check-tokenId">TokenId to check:</label>
                    <input class="p-0.5" bind:value={lentCheckTokenId} type="text" id="lent-check-tokenId" name="lent-check-tokenId">
                    <button class="bg-blue-500 text-white text-lg m-2 p-2.5 rounded-lg" on:click={checkOwner}>Check Owner</button>
                    <label class="p-0.5">Address it belongs to:</label>
                    <label class="p-0.5" id="lent-result">{tokenOwnerDisp}</label>
                </div>
            </div>
        </div>
    </div>
    <div class="container p-3">
        <p class="text-lg text-black font-semibold">Lent NFTs</p>
        <p class="text-sm">Here are the NFTs that you have lent out. You may collect them only after the lending period is over.</p>

        <div class="flex">
            {#each lentNfts as lentNft}
                <div class="flex flex-col bg-gray-100 m-1 p-3 rounded">
                    <p class="p-0.5">Address: {lentNft.address}</p>
                    <p class="p-0.5">Token Id: {lentNft.tokenId}</p>
                    <p class="p-0.5">Current Owner: {lentNft.borrower}</p>
                    <p class="p-0.5">Lend Expiry: {lentNft.expiryDate}</p>
<!--                    <button class="bg-green-400 text-white text-lg m-2 p-2.5 rounded-lg">Collect</button>-->
                </div>
            {/each}
        </div>
    </div>
    <div class="container p-3">
        <p class="text-lg text-black font-semibold">Borrowed NFTs</p>
        <p class="text-sm">Here are the NFTs you have borrowed.</p>
        <div class="flex">
            {#each borrowedNfts as borrowedNft}
                <div class="flex flex-col bg-gray-100 m-1 p-3 rounded">
                    <p class="p-0.5">Address: {borrowedNft.address}</p>
                    <p class="p-0.5">Token Id: {borrowedNft.tokenId}</p>
                    <p class="p-0.5">Expiry: {borrowedNft.expiryDate}</p>
<!--                    <button class="bg-red-400 text-white text-lg m-2 p-2.5 rounded-lg">Return Early</button>-->
                </div>
            {/each}
        </div>
    </div>

</body>
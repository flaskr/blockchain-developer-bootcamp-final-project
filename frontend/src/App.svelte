<script lang="ts" type="module">
    import { ethers } from "./lib/ethers-5.2.esm.min.js";
    let userNfts = [];

    let lentNfts = [];

    let borrowedNfts = [];

    async function loadData() {
        const provider = new ethers.providers.Web3Provider(window.ethereum);
        await provider.send("eth_requestAccounts", []);
        const signer = provider.getSigner();
        const signerAddress = await signer.getAddress();

        userNfts = [{
            address: 'address1',
            tokenId: 1,
        },{
            address: 'address2',
            tokenId: 23,
        }];
        lentNfts = [{
            address: 'address3',
            borrower: 'borrowerAdd2',
            tokenId: 1,
            hasPastExpiry: true,
            expiryDate: 2021
        }];
        borrowedNfts = [{
            address: 'address4',
            tokenId: 998,
            expiryDate: 2023
        }];
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
        <div class="flex flex-col p-2.5 text-center">
            <a>My NFTs</a>
        </div>
        <div class="flex flex-col p-2.5 text-center">
            <a>Verify Owners</a>
        </div>
    </div>
    <div class="container p-3">
        <p class="text-lg text-black font-semibold" >My NFTs</p>
        <p class="text-sm">Here are the NFTs you own - you can lend them out to someone!</p>
        <div class="flex">
            {#each userNfts as userNft}
                <div class="flex flex-col bg-gray-100 m-1 p-3 rounded">
                    <p class="p-0.5">Address: {userNft.address}</p>
                    <p class="p-0.5">Token Id: {userNft.tokenId}</p>
                    <label class="p-0.5" for="lend-to">Address to lend it to:</label>
                    <input class="p-0.5" type="text" id="lend-to" name="lend-to">
                    <button class="bg-gray-400 text-white text-lg p-2.5 rounded-lg">Lend Out</button>
                </div>
            {/each}
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
                    <button class="bg-green-400 text-white text-lg p-2.5 rounded-lg">Collect</button>
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
                    <button class="bg-yellow-600 text-gray-600 text-lg p-2.5 rounded-lg">Return Early</button>
                </div>
            {/each}
        </div>
    </div>

</body>
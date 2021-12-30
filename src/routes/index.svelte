<script>
	// We will need onMount to run checks when page loads
	import { onMount } from 'svelte';
	import { ethers } from 'ethers';

	$: account = null;
	onMount(async () => {
		try {
			// Get ethereum (injected from metamask)
			const { ethereum } = window;
			const provider = new ethers.providers.Web3Provider(ethereum);
			// Signer will be the 'user' of metamask
			const signer = provider.getSigner();
			// alert user if they don't have metamask
			if (!ethereum) {
				alert('Get MetaMask!');
				return;
			}
			// Check to make sure there is an account from
			// metamask
			const accounts = await ethereum.request({ method: 'eth_accounts' });
			if (accounts.length !== 0) {
				account = accounts[0];
			}
		} catch (error) {
			console.log(error);
		}
	});
	// If we don't have accounts we will use this to attach
	// the wallet to the site.
	async function attachWallet() {
		const provider = new ethers.providers.Web3Provider(window.ethereum, 'any');
		// Prompt user for account connections
		await provider.send('eth_requestAccounts', []);
		const signer = provider.getSigner();
		account = await signer.getAddress();
	}
</script>

{#if !account}
	<p>Attach your wallet</p>
	<p>
		<button on:click={attachWallet}>Attach Wallet</button>
	</p>
{:else}
	<p>
		Address Connected: {account}
	</p>
{/if}

<style>
	p {
		text-align: center;
	}
</style>

<script>
    import { onMount } from 'svelte';
    import { EthereumClient, w3mConnectors, w3mProvider } from '@web3modal/ethereum';
    import { Web3Modal } from '@web3modal/html';
    import { configureChains, createConfig, signMessage, getAccount, connect, disconnect } from '@wagmi/core';
    import { goerli } from '@wagmi/core/chains';
    import { InjectedConnector } from '@wagmi/core/connectors/injected';
  
    export let connected;
    const chains = [goerli];
    const projectId = 'edb253b25584ee7baa9632a6a056d76f';
    const { publicClient } = configureChains(chains, [w3mProvider({ projectId })]);
    let account = null;
    const wagmiConfig = createConfig({
      autoConnect: false,
      connectors: w3mConnectors({ projectId, chains }),
      publicClient
    });
  
    const ethereumClient = new EthereumClient(wagmiConfig, chains);
    let web3modal;
    onMount(async () => {
      web3modal = new Web3Modal({ projectId }, ethereumClient);
    });
  
    const connectFunction = async () => {
      const result = await connect({ connector: new InjectedConnector() });
      account = result.account;
      if (account) {
        connected = true;
        sessionStorage.setItem('user', account);
        if (sessionStorage.getItem(`Hash-${account}`)) {
          return;
        } else {
          const signature = await signMessage({ message: 'Please read these terms and conditions carefully before using our services - You need to accept it to proceed- Hash is saved in the memory:)' });
          sessionStorage.setItem(`Hash-${account}`, signature);
        }
      } else {
        connected = false;
      }
    };
  
    const disconnectFunction = async () => {
      const result = await disconnect();
      account = null;
      connected = false;
      sessionStorage.removeItem('user');
    };
  </script>
  
  <div class="w-full pr-2 pl-2 mb-5 lg:pr-10 lg:pl-10 flex justify-between">
    <div class="flex justify-center items-center">
      <img alt="Crypto-logo" class="w-11 h-10" src="/ethereum.png" />
      <h1 class="text-white text-lg font-bold ml-2">PolyTrade</h1>
    </div>
  
    {#if account}
      <button class="bg-gradient-to-r from-cyan-500 to-blue-500 ... hover:from-violet-500 hover:to-red-500 ... text-white font-bold py-2 px-4 rounded-md" on:click={disconnectFunction}>
        Disconnect - {account.substring(0, 5)}
      </button>
    {/if}
    {#if !account}
      <button class="bg-gradient-to-r from-cyan-500 to-blue-500 ... hover:from-violet-500 hover:to-blue-500 ... text-white font-bold py-2 px-4 rounded-md" on:click={connectFunction}>
        Connect Wallet
      </button>
    {/if}
  </div>
  
  <style lang="postcss">
  </style>
  
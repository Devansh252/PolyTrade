<script>
  import MyOrders from '../components/MyOrders.svelte';
  import Nav from '../components/Navbar.svelte';
  import OrderBook from '../components/OrderBook.svelte';

  let connected;

  let activeTab = "orderBook";

  const toggleMyOrders = () => {
    activeTab = "myOrders";
  };

  const toggleOrderBook = () => {
    activeTab = "orderBook";
  };
</script>

<div class="p-4 h-screen bg-gradient-to-b from-slate-900 to-stone-950">
  <Nav bind:connected />
  <div class="m-2 text-white lg:ml-10 lg:mr-10 p-4 h-4/5 rounded-md border border-slate-700 bg-clip-padding backdrop-filter backdrop-blur-sm bg-opacity-10">
    <ul class="flex flex-wrap text-sm font-medium text-center text-gray-500 border-b border-gray-200 dark:border-gray-700 dark:text-gray-400">
      <li class="mr-2">
        <div style={activeTab === "orderBook" ? "border-bottom: 2px solid white; color: white" : ""} on:click={toggleOrderBook} class="inline-block p-4 rounded-t-lg hover:text-gray-600  cursor-pointer">Order Book</div>
      </li>
      <li class="mr-2">
        <div style={activeTab === "myOrders" ? "border-bottom: 2px solid white; color: white" : ""} on:click={toggleMyOrders} class="inline-block p-4 rounded-t-lg hover:text-white cursor-pointer">My Orders</div>
      </li> 
    </ul>

    {#if !connected}
    <div class="flex flex-col items-center h-3/4 justify-center">
      <img alt="connect wallet logo" class="lg:w-1/4 w-1/2 animate-pulse" src="/Crypto.png">
      <h3 class="text-gray-400 text-center">Please Connect your wallet before trading</h3>
    </div>
    {/if}

    {#if connected && activeTab === "orderBook"}
    <OrderBook/>
    {/if}

    {#if connected && activeTab === "myOrders"}
    <MyOrders/>
    {/if}

  </div>
</div>

<style lang="postcss">
  /* Add your styles here */
</style>

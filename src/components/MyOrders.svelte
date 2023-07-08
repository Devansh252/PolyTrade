<script>
    import { onMount } from 'svelte';
    let myOrders ;
    let orderBook;
    let user;
  
    onMount(() =>{

    user = sessionStorage.getItem("user")
    if(!sessionStorage.getItem(user)){
      myOrders = null;
    }
    else{
        orderBook = JSON.parse(sessionStorage.getItem("orders"));
        myOrders =JSON.parse(sessionStorage.getItem(user));
        myOrders = updateOrderStatus();
    }
  })

  // this is the logic for updating my orders status based on orderbook orders
  function updateOrderStatus() {
    myOrders.forEach((order) => {
    const foundOrder = orderBook.find(
      (bookOrder) =>
        bookOrder.orderType === order.orderType &&
        bookOrder.orderBy === order.orderBy &&
        bookOrder.type === order.type &&
        bookOrder.price === order.price &&
        bookOrder.quantity === order.quantity
    );
    
    // if order is not found means it's executed
    if (!foundOrder) {
      order.status = "Executed";
    }
  });
  return myOrders;
}
  </script>

  {#if !myOrders}
<div class="flex justify-center p-10 lg:p-32">
  <h1>
    You don't have any orders :(
  </h1>
</div>
    
  {/if}
  {#if myOrders}
  <div class="h-5/6">
    <div class="flex justify-between items-center w-full border-b border-slate-700 ">
      <span class="p-2 lg:p-5 ">Orders</span>
      <span class="p-2 lg:p-5">Price</span> 
      <span class="p-2 lg:p-5">Quantity</span>
      <span class="p-2 lg:p-5">Status</span>
    </div>
    <div class="h-5/6  overflow-y-scroll">
        {#each myOrders as order}
        <div class="flex justify-between items-center w-full border-b border-slate-700  ">
        <div class="flex items-center w-20 mr-2">
          <img alt="logos" class="w-10 ml-2 mr-2" src ="/{order.orderType}.png">
          <span class="p-2 ">{order.orderType}</span>
        </div>
          <span class="p-2 lg:p-5">{order.price}</span> 
          <span class="p-2 lg:p-5">{order.quantity}</span>
          <span class="p-2 lg:p-5">{order.status}</span>
        </div>
        {/each}
  </div>
  </div>
  {/if}
  
  
  
  <style lang="postcss">  
  </style>
  
<script>
  import { onMount } from 'svelte';
  import ModalButton from "./Modal.svelte"
  import orders from '../Orders';

  let orderBook = orders ;

  onMount(() =>{
  if(!sessionStorage.getItem("orders")){
    sessionStorage.setItem("orders",JSON.stringify(orders));
  }
  orderBook = JSON.parse(sessionStorage.getItem("orders"));
})
  const updateStorage = () => {
  orderBook = JSON.parse(sessionStorage.getItem("orders"));
}

</script>

<div class="h-5/6">
    <div class="flex justify-between items-center w-full border-b border-slate-700 ">
      <span class="p-2 lg:p-5 ">Orders</span>
      <span class="p-2 lg:p-5">Price</span> 
      <span class="p-2 lg:p-5">Quantity</span>
      <span class="p-2 lg:p-5">Action</span>
    </div>
    <div class="h-5/6  overflow-y-scroll">
    {#each orderBook as order}
    <div class="flex justify-between items-center w-full border-b border-slate-700  ">
    <div class="flex items-center w-20 ml-2 mr-6">
      <img alt="crypto" class="w-10 ml-1 mr-2" src ="/{order.orderType}.png">
      <span class="p-2 ">{order.orderType}</span>
    </div>
      <span class="p-2 lg:p-5">{order.price}</span> 
      <span class="p-2 lg:p-5">{order.quantity}</span>
      <span class="p-2 lg:p-5  flex flex-col items-center lg:flex-row "><ModalButton type="Buy"  updateStorage={updateStorage} orderType={order.orderType} /><ModalButton type="Sell" updateStorage={updateStorage} orderType={order.orderType} /></span>
    </div>
    {/each}
  </div>
  </div>


  <style lang="postcss">  
  </style>

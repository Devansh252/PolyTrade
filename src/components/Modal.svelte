<script>
  import { onMount } from "svelte";
  import orders from "../Orders";

  export let orderType, type, updateStorage;

  let price;
  let value;
  let newOrder = orders;

  let user = sessionStorage.getItem("user");
  let myOrders = JSON.parse(sessionStorage.getItem(user)) || [];
  let isOpen = false;
  let isOpenExecuteModal = false;
  let matchingOrders;

  onMount(() => {
    user = sessionStorage.getItem("user");
  });

  

  function openExecuteModal () {
    isOpenExecuteModal = true;
  }

  function openModal() {
    isOpen = true;
  }

  function closeExecuteModal (){
    isOpenExecuteModal = false;
  }
  
  function closeModal() {
    isOpen = false;

  }
 
  const  handleConfirm = () => {
  // Retrieve the opposite order type
  const oppositeOrderType = type === "Buy" ? "Sell" : "Buy";
  
  // Filter the existing orders by opposite order type and matching size
   matchingOrders = newOrder.filter(
    (order) =>
      order.type === oppositeOrderType &&
      order.orderType === orderType &&
      order.orderBy !== user &&
      order.quantity === value && 
      ((type === "Buy" && order.price <= price) ||
        (type === "Sell" && order.price >= price))
  );

  if (matchingOrders.length > 0) {
    // Opening Modal with execute button
    openExecuteModal();    
  }

  else{
    myOrders.push({
      orderType: orderType,
      orderBy: user,
      type: type,
      price: price,
      quantity: value,
      status: "Pending",
    });
    newOrder.push({
      orderType: orderType,
      orderBy: sessionStorage.getItem("user"),
      type: type,
      price: price,
      quantity: value,
    });

    // Update session storage
    sessionStorage.setItem(user, JSON.stringify(myOrders));
    sessionStorage.setItem("orders", JSON.stringify(newOrder));

    updateStorage();

    // Reset the input fields
    price = "";
    value = "";
  }
  closeModal();
}

const  handleCancel = () => {
    closeModal();
  }


  const handleExecute = () => {
    matchingOrders.sort((a, b) => {
      if (type === "Buy") {
        return b.price - a.price;
      } else {
        return a.price - b.price;
      }
    });

    // Get the most profitable and oldest matching order
    const matchedOrder = matchingOrders[0];

    // Remove the matched order from the order book
    const matchedIndex = newOrder.indexOf(matchedOrder);
    if (matchedIndex !== -1) {
      newOrder.splice(matchedIndex, 1);
    }

    // Update the user's order status
    myOrders.push({
      orderType: orderType,
      orderBy: user,
      type: type,
      price: price,
      quantity: value,
      status: "Executed",
    });


    // Update session storage
    sessionStorage.setItem(user, JSON.stringify(myOrders));
    sessionStorage.setItem("orders", JSON.stringify(newOrder));

    // Call the updateStorage function if provided
    
    updateStorage();
  
    // Reset the input fields
    price = "";
    value = "";

    closeExecuteModal();

  }

</script>

{#if type === "Buy"}
<button class="hover:bg-green-900 text-white font-bold py-2 px-4 rounded-md border border-slate-700 mr-2" on:click={openModal}>
  {type}
</button>
{/if}

{#if type === "Sell"}
<button class="hover:bg-red-900 text-white font-bold py-2 px-4 rounded-md border border-slate-700 mr-2" on:click={openModal}>
  {type}
</button>
{/if}

{#if isOpen}
<div class="fixed top-0 left-0 right-0 bottom-0 flex items-center justify-center z-50">
  <div class="fixed inset-0 bg-black opacity-50"></div>
  <div class="bg-slate-950 border border-slate-700 p-4 rounded-lg shadow-md relative lg:w-1/4">
    <h2 class="text-lg font-bold mb-4 w-full text-center">Place Trade</h2>

    <div class="mb-6">
      <label class="block mb-2 text-sm font-medium text-white">Price</label>
      <input bind:value={price} type="number" class="bg-gray-950 border border-slate-600 text-white text-sm rounded-lg block w-full p-2.5">
    </div>

    <div class="mb-6">
      <label class="block mb-2 text-sm font-medium text-white">Value</label>
      <input bind:value={value} type="number" class="bg-gray-950 border border-slate-600 text-white text-sm rounded-lg block w-full p-2.5">
    </div>

    <div class="flex flex-col">
      <button class="bg-blue-500 hover:bg-blue-700 text-white m-1 font-bold py-2 px-4 rounded mr-2" on:click={handleConfirm}>Place Trade</button>
      <button class="bg-gray-300 hover:bg-gray-400 m-1 text-gray-800 font-bold py-2 px-4 rounded" on:click={handleCancel}>
        Cancel
      </button>
    </div>
  </div>
</div>
{/if}

{#if isOpenExecuteModal}

<div class="fixed top-0 left-0 right-0 bottom-0 flex items-center justify-center z-50">
  <div class="fixed inset-0 bg-black opacity-50"></div>
  <div class="bg-slate-950 border flex justify-center flex-col border-slate-700 p-4 rounded-lg shadow-md relative lg:w-1/4">
    <h2 class="text-lg font-bold mb-4 w-full text-center">Execute Trade</h2>
    <img src="/found.png">
   <p class="text-center mb-10">Found a Trade that you are Lookig for!</p>
    <div class="flex flex-col">
      <button class="bg-blue-500 hover:bg-blue-700 text-white m-1 font-bold py-2 px-4 rounded mr-2" on:click={handleExecute}>Execute Trade</button>
    </div>
  </div>
</div>

{/if}

<style>
  .bg-black {
    background-color: rgba(0, 0, 0, 0.801);
  }
</style>

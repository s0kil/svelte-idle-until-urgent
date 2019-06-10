<script>
  import { onMount, tick } from "svelte";
  import makeIdleGetter from "idle-until-urgent";

  import Item from "./components/Item.svelte";

  let success, data;
  let itemsAmount = 5;
  let getMoreItems = null;
  let itemsLoaded = 0;

  function getItems(itemsAmount) {
    const apiUrl = `https://cors-anywhere.herokuapp.com/https://shibe.online/api/shibes?count=${itemsAmount}`;
    return fetch(apiUrl).then(_ => _.json());
  }

  onMount(async () => {
    data = await getItems(itemsAmount);

    await tick();
    getMoreItems = makeIdleGetter(() => getItems(itemsAmount));
  });
</script>

{#if data}
  <p>{itemsAmount} Items</p>
  <hr />
  {#each data as item}
    <Item
      on:load={() => {
        itemsLoaded++;
      }}
      url={item} />
  {/each}
{/if}

{#if getMoreItems && itemsLoaded === itemsAmount}
  {#await getMoreItems()}
    <p>waiting for more items</p>
  {:then data}
    <hr />
    <p>{itemsAmount} More</p>
    {#each data as item}
      <Item url={item} />
    {/each}
  {:catch error}
    <p>Something went wrong: {error.message}</p>
  {/await}
{/if}

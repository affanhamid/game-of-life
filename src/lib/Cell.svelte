<script>
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();
  export let alive;

  const handleClick = () => {
    dispatch("click");
  };
</script>

<div
  class="cell"
  style="background-color:{alive ? 'black' : 'white'}"
  on:click={() => handleClick()}
></div>

<style>
  .cell {
    width: 40px;
    aspect-ratio: 1;
    border: 1px solid black;
  }
</style>

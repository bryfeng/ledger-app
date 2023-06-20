<script lang="ts">
    import { onMount } from 'svelte';
    let svgs: string[] = [];

    onMount(async () => {
    const response = await fetch('src/components/assets/paymethods/svg.json');
    svgs = await response.json();
  });

</script>
  
  <div class="svg-container">
    {#each svgs as svg (svg)}
      <img src="src/components/assets/paymethods/{svg}" alt={svg} class="svg-item" />
    {/each}
  </div>
  
  <style>
    .svg-container {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
    }
  
    .svg-item {
      width: 50px;
      height: 50px;
      margin: 0 10px;
    }
  </style>
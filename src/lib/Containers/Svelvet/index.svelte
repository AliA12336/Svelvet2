<!--Note: Import / export in svelvet is used for passing props  -->
<script lang="ts">
  import GraphView from '$lib/Containers/GraphView/index.svelte';
  import { findOrCreateStore } from '$lib/stores/store';
  import { afterUpdate, onMount } from 'svelte';
  import type { Node, Edge } from '$lib/types/index.js';
  
  // Declaring variables for Svelvet components which will be usable in other files
  export let nodes: Node[];
  export let edges: Edge[];
  export let width: number = 600;
  export let height: number = 600;
  export let background: boolean = false;


  // generates a unique string for each svelvet component's unique store instance
  const key = (Math.random() + 1).toString(36).substring(7);
  // creates a store that uses the unique sting as the key to create and look up the corresponding store
  // this way we can have multiple Svelvet Components on the same page and prevent overlap of information
  const svelvetStore = findOrCreateStore(key);
  // stores (state) within stores, so that we cannot access values from everywhere
  const { widthStore, heightStore, nodesStore, derivedEdges } = svelvetStore;

  // sets the state of the store to the values passed in from the Svelvet Component on initial render
  onMount(() => {
    svelvetStore.nodesStore.set(nodes);
    svelvetStore.edgesStore.set(edges);
    svelvetStore.widthStore.set(width);
    svelvetStore.heightStore.set(height);
    svelvetStore.backgroundStore.set(background);
  });
  // enables data reactivity
  afterUpdate(() => {
    svelvetStore.nodesStore.set(nodes);
    svelvetStore.edgesStore.set(edges);
    svelvetStore.widthStore.set(width);
    svelvetStore.heightStore.set(height);
    svelvetStore.backgroundStore.set(background);
  });
</script>

<!-- Now that a store has been created from the initial nodes and initial edges we drill props from the store down to the D3 GraphView along with the unique key -->
<div class="Svelvet" style={`width: ${$widthStore}px; height: ${$heightStore}px`}>
  <GraphView {nodesStore} {derivedEdges} {key} />
</div>

<style>
  .Svelvet {
    position: relative;
    overflow: hidden;
    display: grid;
    font-family: 'Segoe UI', sans-serif;
    background-color: 'white';
  }
</style>

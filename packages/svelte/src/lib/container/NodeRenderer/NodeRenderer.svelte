<script lang="ts">
  import { onDestroy } from 'svelte';

  import { NodeWrapper } from '$lib/components/NodeWrapper';
  import { useStore } from '$lib/store';

  const { nodes, updateNodeDimensions } = useStore();
  const resizeObserver: ResizeObserver | null =
    typeof ResizeObserver === 'undefined'
      ? null
      : new ResizeObserver((entries: ResizeObserverEntry[]) => {
          const updates = entries.map((entry: ResizeObserverEntry) => ({
            id: entry.target.getAttribute('data-id') as string,
            nodeElement: entry.target as HTMLDivElement,
            forceUpdate: true
          }));

          updateNodeDimensions(updates);
        });

  onDestroy(() => {
    resizeObserver?.disconnect();
  });

</script>

<div class="svelte-flow__nodes">
  {#each $nodes as node (node.id)}
    <NodeWrapper
      {...node}
      {resizeObserver}
      on:node:click
      on:node:mouseenter
      on:node:mousemove
      on:node:mouseleave
      on:connect:start
      on:connect
      on:connect:end
    />
  {/each}
</div>

<style>
  .svelte-flow__nodes {
    width: 100%;
    height: 100%;
    pointer-events: none;
    transform-origin: 0 0;
  }
</style>
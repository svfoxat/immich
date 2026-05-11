<script lang="ts">
  import { cleanClass } from '$lib';
  import BrokenAsset from '$lib/components/assets/broken-asset.svelte';

  interface Props {
    alt?: string;
    preload?: boolean;
    src: string;
    class?: string;
  }

  let { alt, preload = false, src, class: className }: Props = $props();

  let isBroken = $state(false);
</script>

{#if isBroken}
  <BrokenAsset hideMessage class="aspect-square" />
{:else}
  <img
    {alt}
    onerror={() => (isBroken = true)}
    class={cleanClass('object-cover', className)}
    style="aspect-ratio: 9/16; object-position: center"
    data-testid="album-image"
    draggable="false"
    loading={preload ? 'eager' : 'lazy'}
    {src}
  />
{/if}

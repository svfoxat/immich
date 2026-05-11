<script lang="ts">
  import AssetCover from '$lib/components/sharedlinks-page/covers/asset-cover.svelte';
  import NoCover from '$lib/components/sharedlinks-page/covers/no-cover.svelte';
  import { getAssetMediaUrl } from '$lib/utils';
  import { type AlbumResponseDto } from '@immich/sdk';
  import { t } from 'svelte-i18n';

  interface Props {
    album: AlbumResponseDto;
    preload?: boolean;
    class?: string;
  }

  let { album, preload = false, class: className }: Props = $props();

  let alt = $derived(album.albumName || $t('unnamed_album'));
  let thumbnailUrl = $derived(
    album.albumThumbnailAssetId ? getAssetMediaUrl({ id: album.albumThumbnailAssetId }) : null,
  );
  let fullUrl = $derived(
    album["assets"][0].originalFileName
  )

  // this works!
  console.log(album["assets"])
</script>

{#if fullUrl}
  <AssetCover {alt} class={className} src={fullUrl} />
{:else if thumbnailUrl}
  <AssetCover {alt} class={className} src={thumbnailUrl} {preload} />
{:else}
  <NoCover {alt} class={className} {preload} />
{/if}

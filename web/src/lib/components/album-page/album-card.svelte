<script lang="ts">
  import AlbumCover from '$lib/components/album-page/album-cover.svelte';
  import { user } from '$lib/stores/user.store';
  import { getContextMenuPositionFromEvent, type ContextMenuPosition } from '$lib/utils/context-menu';
  import { getShortDateRange } from '$lib/utils/date-time';
  import {type AlbumResponseDto, getAlbumInfo, searchAssets} from '@immich/sdk';
  import { IconButton } from '@immich/ui';
  import { mdiDotsVertical } from '@mdi/js';
  import { t } from 'svelte-i18n';

  interface Props {
    album: AlbumResponseDto;
    showOwner?: boolean;
    showDateRange?: boolean;
    showItemCount?: boolean;
    preload?: boolean;
    onShowContextMenu?: ((position: ContextMenuPosition) => unknown) | undefined;
  }
  let {
    album,
    showOwner = false,
    showDateRange = false,
    showItemCount = false,
    preload = false,
    onShowContextMenu = undefined,
  }: Props = $props();

  let albumDetail = $state(album);

  async function loadAlbumDetail() {
    await getAlbumInfo({
      id: album.id,
    }).then((data) => {
        albumDetail = data
      });
  }

  loadAlbumDetail()
  const showAlbumContextMenu = (e: MouseEvent) => {
    e.stopPropagation();
    e.preventDefault();
    onShowContextMenu?.(getContextMenuPositionFromEvent(e));
  };
</script>

<div
  class="group relative border border-transparent hover:bg-gray-100 hover:border-gray-200 dark:hover:border-gray-800 dark:hover:bg-gray-900"
  data-testid="album-card"
>
  {#if onShowContextMenu}
    <div
      id="icon-{albumDetail.id}"
      class="absolute end-6 top-6 opacity-0 group-hover:opacity-100 focus-within:opacity-100"
      data-testid="context-button-parent"
    >
      <IconButton
        color="secondary"
        aria-label={$t('show_album_options')}
        icon={mdiDotsVertical}
        shape="round"
        variant="filled"
        size="medium"
        class="icon-white-drop-shadow"
        onclick={showAlbumContextMenu}
      />
    </div>
  {/if}

  <AlbumCover album={albumDetail} {preload} class="transition-all duration-300 hover:shadow-lg" />

  <div class="backdrop-blur p-1">
    <p
      class="w-full leading-6 text-md line-clamp-1 font-semibold text-black dark:text-white group-hover:text-primary"
      data-testid="album-name"
      title={albumDetail.albumName}
    >
      {album.albumName}
    </p>


    <span class="flex gap-2 text-sm dark:text-immich-dark-fg" data-testid="album-details">
    {#if showDateRange && albumDetail.startDate && albumDetail.endDate}
      <p class="flex text-sm dark:text-immich-dark-fg capitalize">
        {getShortDateRange(albumDetail.startDate, albumDetail.endDate)}
      </p>
    {/if}
      {#if showItemCount}
        <p>
          {$t('items_count', { values: { count: albumDetail.assetCount } })}
        </p>
      {/if}

      {#if (showOwner || albumDetail.shared) && showItemCount}
        <p>•</p>
      {/if}

      {#if showOwner}
        {#if $user.id === album.ownerId}
          <p>{$t('owned')}</p>
        {:else if album.owner}
          <p>{$t('shared_by_user', { values: { user: album.owner.name } })}</p>
        {:else}
          <p>{$t('shared')}</p>
        {/if}
      {:else if albumDetail.shared}
        <p>{$t('shared')}</p>
      {/if}
    </span>
  </div>
</div>

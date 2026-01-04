<script lang="ts">
  import { db } from '$lib/catalog/db';
  import { updateProgress } from '$lib/settings';
  import { nav, routeParams } from '$lib/util/hash-router';
  import { onMount } from 'svelte';

  let mangaName = $derived($routeParams.manga_name || '');
  let volumeName = $derived($routeParams.volume_name || '');
  let pageNumber = $derived($routeParams.page_number || 1);

  onMount(async () => {
    const volumes = await db.volumes.toArray();

    const matchingMangaVolumes = volumes.filter((vol) => vol.series_title === mangaName);

    if (matchingMangaVolumes) {
      const matchingVolume = matchingMangaVolumes.find(
        (vol) => vol.volume_title.trim() === volumeName.trim()
      );

      if (matchingVolume) {
        updateProgress(matchingVolume.volume_uuid, pageNumber);
        nav.toReader(matchingVolume.series_uuid, matchingVolume.volume_uuid);
      }
    }
  });
</script>

<svelte:head>
  <title>Mokuro</title>
</svelte:head>

<div class="h-[90svh] p-2">Loading...</div>

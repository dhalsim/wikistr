<script lang="ts">
  import type { NostrEvent } from 'nostr-tools/pure';

  import UserLabel from './UserLabel.svelte';
  import { formatDate } from '$lib/utils';

  export let openArticle: (event: NostrEvent, ev: MouseEvent) => void;
  export let event: NostrEvent;

  $: plainText = event.content
    .slice(0, 210)
    .replace(/\[\[(.*?)\]\]/g, (_: any, content: any) => {
      return content;
    })
    .slice(0, 190);

  function handleClick(ev: MouseEvent) {
    openArticle(event, ev);
  }
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<div
  on:mouseup|preventDefault={handleClick}
  class="cursor-pointer p-4 bg-white border-2 border-stone-200 hover:bg-stone-50 rounded-lg mt-2"
>
  <h1>
    {event.tags.find((e) => e[0] == 'title')?.[0] && event.tags.find((e) => e[0] == 'title')?.[1]
      ? event.tags.find((e) => e[0] == 'title')?.[1]
      : event.tags.find((e) => e[0] == 'd')?.[1]}
  </h1>
  <p class="text-xs my-1">
    by <span class="-ml-1"><UserLabel pubkey={event.pubkey} /></span>
    {formatDate(event.created_at)}
  </p>
  <p class="text-xs text-wrap break-words whitespace-pre-wrap">
    {#if event.tags.find((e) => e[0] == 'summary')?.[0] && event.tags.find((e) => e[0] == 'summary')?.[1]}
      {event.tags
        .find((e) => e[0] == 'summary')?.[1]
        .slice(
          0,
          192
        )}{#if String(event.tags.find((e) => e[0] == 'summary')?.[1])?.length > 192}...{/if}
    {:else}
      {plainText.length <= 170 ? plainText : plainText.substring(0, 167) + '...'}
    {/if}
  </p>
</div>

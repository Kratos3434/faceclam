<script lang="ts">
	import type { LayoutData } from "../../$types";
	import ChatStatus from "$lib/ChatStatus.svelte";
  import { onlineUsers } from "$lib";
	import type { FriendProps } from "../../../../../types";

  $: onlineFriends = () => {
    const result: FriendProps[] = [];
    data.friends?.map((e: FriendProps) => {
      if (e.user.id === data.currentUser.id) {
        if ($onlineUsers.get(e.friend.email)) {
          result.push(e);
        }
      } else {
        if ($onlineUsers.get(e.user.email)) {
          result.push(e);
        }
      }
    })

    return result;
  }
  export let data: LayoutData;
</script>

<div class="tw-px-[16px]">
  <div class="tw-text-[15px] tw-py-2">ALL FRIENDS - {onlineFriends().length}</div>
  <hr class="tw-border-black" />
  <!-- Array of all friends -->
  <div class="tw-flex tw-flex-col tw-gap-2 tw-py-2">
    {#if onlineFriends().length === 0}
      <p>No online friends</p>
    {:else if data.friends}
      {#each data.friends as friend }
        {#if friend.user.id === data.currentUser.id}
          {#if $onlineUsers.get(friend.friend.email)}
            <ChatStatus user={friend.friend} isActive={true} />
          {/if}
        {:else}
          {#if $onlineUsers.get(friend.user.email)}
            <ChatStatus user={friend.user} isActive={true} />
          {/if}
        {/if}
      {/each}
    {/if}
  </div>
</div>
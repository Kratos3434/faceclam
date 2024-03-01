<script lang="ts">
	import { browser } from "$app/environment";
	import { openAddPost, openAddStatus, openPopup, viewLikes, viewPost } from "$lib";
	import ViewLikes from "$lib/ViewLikes.svelte";
  import "../app.css";
  import { QueryClient, QueryClientProvider } from '@tanstack/svelte-query';
  import type { LayoutData } from "./$types";
	import ViewPost from "$lib/ViewPost.svelte";
	import AddStatus from "$lib/AddStatus.svelte";
	import AddPost from "$lib/AddPost.svelte";
	import SigninPopUpModal from "$lib/SigninPopUpModal.svelte";
	import { onMount } from "svelte";
	import { socket } from "../socket";

  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        enabled: browser
      }
    }
  });

  onMount(() => {
    console.log('reconnect event')
    const reconnect = () => {
      socket.connect();
      socket.emit('join', {
        email: data.currentUser.email
      })
    }

    socket.on('reconnect', reconnect);

    return () => socket.off('reconnect', reconnect);
  })
  export let data: LayoutData;
</script>

<QueryClientProvider client={queryClient}>
  <slot />
  {#if $viewLikes.status}
    <ViewLikes currentUser={data.currentUser} />
  {/if}

  {#if $viewPost.status}
    <ViewPost currentUser={data.currentUser} token={data.token} />
  {/if}

  {#if $openAddStatus}
    <AddStatus user={data.currentUser} token={data.token} />
  {/if}

  {#if $openAddPost}
    <AddPost user={data.currentUser} token={data.token} />
  {/if}

  {#if !data.loggedIn && $openPopup}
    <SigninPopUpModal />
  {/if}
</QueryClientProvider>
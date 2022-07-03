<script lang="ts">
	import {supabase} from "./supabaseClient"
	import {readable, get} from 'svelte/store'
  import "../public/tailwind.css"

	async function addPost(e) {
    const formData = new FormData(e.target)
    const {} = await supabase
      .from('Message')
      .insert({
        txtmessage: formData.get('message'),
      })
  }

  const Message = readable(null, (set) => {
    supabase
      .from('Message')
      .select('*')
      .then(({error, data}) => set(data))
    // add subscription to supabase logic
    const subscription = supabase
      .from('Message')
      .on('INSERT', (payload) => {
        // payload.evenType
        // payload.new
        // payload.old
        set([...get(Message), payload.new]) 
      })
      .subscribe()
    return () => supabase.removeSubscription(subscription)
  })
  
</script>

<main style="display: flex; flex-direction: column; align-items:center;">
	<h2>SEND SECRET MESSAGE</h2>
	<form on:submit|preventDefault={addPost}>
		<textarea name=message></textarea> <br>
	  <button>Send</button>
	</form>
	{#if $Message}
	  {#each $Message.reverse() as {txtmessage}}
		<p class="">{txtmessage}</p>
	  {/each}
	{:else}
	  Loading..
	{/if}
  </main>

<style>
	
</style>
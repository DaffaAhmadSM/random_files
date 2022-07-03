<script>
	import {supabase} from "./supabaseClient"
	import {readable, get} from 'svelte/store'
  import Time from "svelte-time";
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
  <div class="flex mx-auto items-center justify-center shadow-lg mx-8 mb-4 max-w-lg">
    <form on:submit|preventDefault={addPost} class="w-full max-w-xl bg-white rounded-lg px-4 pt-2">
       <div class="flex flex-wrap -mx-3 mb-6">
          <h2 class="px-4 pt-3 pb-2 text-gray-800 text-lg">Send a Secret Message</h2>
          <div class="w-full md:w-full px-3 mb-2 mt-2">
             <textarea class="bg-gray-100 rounded border border-gray-400 leading-normal resize-none w-full h-20 py-2 px-3 font-medium placeholder-gray-700 focus:outline-none focus:bg-white" name="message" required></textarea>
          </div>
             <div class="w-full md:w-full flex items-start md:w-full px-3 -mr-1">
                <button type='submit' class="bg-white text-gray-700 font-medium py-1 px-4 border border-gray-400 rounded-lg tracking-wide mr-1 hover:bg-gray-100">Send</button>
             </div>
          </div>
       </form>
    </div>
	{#if $Message}
	  {#each $Message.reverse() as {txtmessage, created_at}}
		<div class="p-1 w-3/4">
      <div class="card m-2 cursor-pointer border border-gray-400 rounded-lg hover:shadow-md hover:border-opacity-0 transform hover:-translate-y-1 transition-all duration-200">
        <div class="m-3">
        <p class="font-light font-mono text-lg text-gray-700 hover:text-gray-900 transition-all duration-200 text-center">{txtmessage}</p>
        <Time timestamp={created_at} class="font-light font-mono text-xs text-gray-700 hover:text-gray-900 transition-all duration-200"/>
        </div>
      </div>
    </div>
	  {/each}
	{:else}
	  Loading..
	{/if}
  </main>

<style>
	
</style>
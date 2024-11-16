<script>
  import { DB } from '../db.js';
  let { filePath = "", item = {}, close = () => {} } = $props();
  let content = $state("");
  let saved = $state(true);

  // Fetch content from DB
  $effect(() => {
    content = DB.get(filePath) || "";
    saved = true; // Initially, the content is saved
  });

  function saveContent() {
    if(item.permission === "r"){
      alert("You don't have permission to edit this file");
      return;
    }

    DB.set(filePath, content); // Save content to DB
    saved = true; // Update the saved state
  }

  // Watch for changes in content to update the saved state
  $effect(() => {
    saved = DB.get(filePath) === content;
  });
</script>

<!-- Editor Modal -->
{#if filePath}
  <div class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50">
    <div class="bg-gray-600 p-6 w-6/12 h-5/6 rounded-2xl">
      <div class="card-header flex justify-between">
      <h2 class="text-xl font-semibold mb-4 text-white">Edit File: {filePath}</h2>
      <span>permision: {item.permission}</span>
    </div>
      <textarea 
        bind:value={content} 
        class="w-full h-5/6 border border-gray-300 p-2"
        placeholder="Edit your file content here..."
      ></textarea>

      <div class="mt-4 flex justify-end">
        <button onclick={saveContent} class="text-white px-4 py-2 rounded mr-2 {saved ? 'bg-green-500' : 'bg-blue-500'}">
          {saved ? "Saved" : "Save"}
        </button>
        <button onclick={() => close()} class="bg-gray-500 text-white px-4 py-2 rounded">Cancel</button>
      </div>
    </div>
  </div>
{/if}

<style>
  /* Add modal styling if necessary */
  textarea {
    resize: none;
  }
</style>

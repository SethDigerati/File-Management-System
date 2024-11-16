<script>
  import { DB } from '../db.js';
  let { filePath = "", close = () => {} } = $props();
  let content = $state("");
  let saved = $state(true);

  // Fetch content from DB
  $effect(() => {
    content = DB.get(filePath) || "";
    saved = true; // Initially, the content is saved
  });

  function saveContent() {
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
    <div class="bg-white p-6 w-6/12 h-4/6 p-2">
      <h2 class="text-xl font-semibold mb-4">Edit File: {filePath}</h2>
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

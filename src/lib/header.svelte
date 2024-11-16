<script>
  import { createEventDispatcher } from "svelte";

  let { path = [] } = $props();

  const dispatch = createEventDispatcher();

  function handleNewFolder() {
    dispatch("newFolder");
  }

 

  function handleSearch() {
    dispatch("search");
  }

  function handleBack() {
    dispatch("back");
  }

  function handleNewFile() {
    dispatch("newFile");
  }
</script>

<nav
  class="flex p-8 gap-4 justify-around max-w-[800px] mx-auto my-4 border-2 rounded-lg"
>
  <div>
    <button
      class="bg-blue-500 hover:bg-blue-700 text-white text-sm md:text-base font-bold py-2 px-2 md:px-4 rounded"
      on:click={handleNewFolder}
    >
      New folder
    </button>
  </div>
  <div>
    <button
      class="bg-purple-500 hover:bg-purple-700 text-white text-sm md:text-base  font-bold py-2 px-2 md:px-4 rounded"
      on:click={handleNewFile}
    >
      New file
    </button>
  </div>
<div class:hidden={path.length  == 1}>
    <button
        class="bg-red-500 hover:bg-red-700 text-white text-sm md:text-base  font-bold py-2 px-2 md:px-4 rounded "
        on:click={handleBack}
    >
        Back
    </button>
</div>
 
  <div>
    <button
      class="bg-gray-500 hover:bg-gray-700 text-white text-sm md:text-base  font-bold py-2 px-2 md:px-4 rounded"
      on:click={handleSearch}
    >
      Search
    </button>
  </div>
</nav>

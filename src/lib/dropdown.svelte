<script>
    import { createEventDispatcher } from 'svelte';
  
     let {options = []} = $props();
  
    const dispatch = createEventDispatcher();
  
 
  
    function selectOption(option) {
      dispatch('optionSelect', option);
    }

    
  </script>
  
  <div class="relative">
      <ul class="absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg">
        {#each options as option}
          <li 
            class="px-4 py-2 hover:bg-gray-200 cursor-pointer rounded-md" 
            on:click={() => selectOption(option)}
          >
            {option.label}
          </li>
        {/each}
      </ul>
  </div>
  
  <style>
    /* Custom styles or rely on TailwindCSS */
  </style>
  
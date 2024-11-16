<script>
  import FolderIcon from './icon/folder.svelte';
  import FileIcon from './icon/file.svelte';
  import EllipsisIcon from './icon/ellipsis.svelte';
  import Dropdown from './dropdown.svelte';

  
  let {item, isFile, clicked = ()=>{},  onRename= ()=>{}, onDelete = ()=>{}, onPermissions= ()=>{}} = $props();

  const dropdownOptions = [
    { label: 'Rename', value: 'rename' },
    { label: 'Delete', value: 'delete' },
    { label: 'Permissions', value: 'permissions' }
  ];

  function toggleDropdown(event) {
    const dropdown = event.currentTarget.nextElementSibling;
    dropdown.classList.toggle('hidden');
  }

  function handleOptionSelect(event) {
    const option = event.detail;
    switch (option.value) {
      case 'rename':
        onRename(item, isFile);
        break;
      case 'delete':
        onDelete(item, isFile);
        break;
      case 'permissions':
        onPermissions(item, isFile);
        break;
    }
  }

</script>
<div class="relative"></div>
  <div class="flex items-center p-4 bg-gray-700 rounded-lg hover:bg-gray-600" ondblclick={()=>{clicked(item, isFile)}} >
    {#if isFile}
      <FileIcon class="w-10 h-10 text-white mr-2" />
    {:else}
      <FolderIcon class="w-10 h-10 text-white mr-2" />
    {/if}
    <span class="text-white flex-1">{item.name}</span>
    <span class="text-gray-400 mr-2">Permission: {item.permission}</span>
    <button onfocus={toggleDropdown} onfocusout={toggleDropdown} class="text-white">
      <EllipsisIcon class="w-6 h-6" />
    </button>
    <div class="dropdown absolute right-0 mt-2 w-48 bg-white z-40 rounded-md shadow-lg hidden hover:block">
      <Dropdown options={dropdownOptions} on:optionSelect={handleOptionSelect} />
    </div>
  </div>

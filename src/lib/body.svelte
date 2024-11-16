<script>
  //@ts-nocheck
  import { DB } from "../db.js";
  import Editor from "./editor.svelte";
  import Item from "./item.svelte";

  // Props: tree (for rendering), and path (to know where we are)
  let { tree, path = [] , updated} = $props();


  let currentEditing = $state("");

  // Function to fetch the entire tree from the DB
  function fetchTreeFromDB() {
    return DB.get("fileTree") || { name: "root", children: [], files: [] };
  }

  // Helper function to locate the current directory in the DB tree based on the path
  function findCurrentDirInDBTree(dbTree) {
    let currentDir = dbTree;
    for (let i = 1; i < path.length; i++) {
      currentDir = currentDir.children.find(
        (folder) => folder.name === path[i]
      );
      console.log(path[i], currentDir);
      if (!currentDir) {
        break; // If the path is invalid, return the root
      }
    }
    return currentDir || dbTree;
  }

  // Update the file tree in the DB without overwriting unrelated parts
  function saveTreeToDB(dbTree, currentDir) {
    tree = currentDir; // Update the tree in the component
    DB.set("fileTree", dbTree);
    updated();
  }

 
  // Function to delete an item (file or folder) in the current directory in the DB
  function deleteItem(item, isFile) {
    let dbTree = fetchTreeFromDB(); // Fetch the complete tree from the DB
    let currentDir = findCurrentDirInDBTree(dbTree); // Find the current directory in the DB tree

    // Compare using a unique identifier, such as 'name'
    const index = isFile
      ? currentDir.files.findIndex((f) => f.name === item.name)
      : currentDir.children.findIndex((f) => f.name === item.name);


    if (index > -1) {
      if (isFile) {
        currentDir.files.splice(index, 1);
      } else {
        currentDir.children.splice(index, 1);
      }

      saveTreeToDB(dbTree, currentDir); // Save the updated DB tree back
    } else {
      console.log("Item not found in the current directory.");
    }
  }


// Function to rename an item (file or folder) in the DB
function renameItem(item, isFile) {
  const newName = prompt("Enter new name:", item.name).trim();
  if (!newName) return;

  let dbTree = fetchTreeFromDB(); // Fetch the complete tree from the DB
  let currentDir = findCurrentDirInDBTree(dbTree); // Find the current directory in the DB tree

  // Check for duplicate names in the current directory
  const isDuplicate = currentDir.children.some((folder) => folder.name === newName) ||
    currentDir.files.some((file) => file.name === newName);

  if (!isDuplicate) {
    // Find and update the item by comparing using 'name' or 'id'
    const updateItem = isFile 
      ? currentDir.files.find((file) => file.name === item.name) 
      : currentDir.children.find((folder) => folder.name === item.name);
    
    if (updateItem) {
      updateItem.name = newName; // Update the name of the item
    }


    saveTreeToDB(dbTree, currentDir); // Save the updated DB tree back
  } else {
    alert("An item with this name already exists.");
  }
}

// Function to handle permissions change in the DB
function changePermissions(item, isFile) {
  const newPermissions = prompt(`Set new permissions for ${item.name}:`);
  if (newPermissions) {
    let dbTree = fetchTreeFromDB(); // Fetch the complete tree from the DB
    let currentDir = findCurrentDirInDBTree(dbTree); // Find the current directory in the DB tree

    // Find the item to update by comparing its name
    const updateItem = isFile 
      ? currentDir.files.find((file) => file.name === item.name)
      : currentDir.children.find((folder) => folder.name === item.name);
    
    if (updateItem) {
      updateItem.permissions = newPermissions; // Update permissions for the item
      saveTreeToDB(dbTree, currentDir); // Save the updated DB tree back
    }
  }
}


  // Function to handle click on folder to navigate into it
  function handleClicked(item, isFile) {
    if (!isFile) {
      path.push(item.name); // Navigate deeper into the directory
    }else{
        let filepath = path.join('/') + '/' + item.name;
        currentEditing = filepath;
        console.log(currentEditing);
    }
  }

  function closeEditor(){
    currentEditing = "";
  }
</script>

{#if currentEditing !== ""}
<Editor filePath={currentEditing} close={closeEditor} />
{/if}


<!-- Render the current directory and its items -->
<div class="p-4">
  <ul class="space-y-2">
    {#each tree.children as item (item.name)}
      <li>
        <Item
          {item}
          isFile={false}
          onRename={renameItem}
          onDelete={deleteItem}
          onPermissions={changePermissions}
          clicked={handleClicked}
        />
      </li>
    {/each}
    {#each tree.files as file (file.name)}
      <li>
        <Item
          item={file}
          isFile={true}
          onRename={renameItem}
          onDelete={deleteItem}
          onPermissions={changePermissions}
          clicked={handleClicked}
        />
      </li>
    {/each}
  </ul>
</div>

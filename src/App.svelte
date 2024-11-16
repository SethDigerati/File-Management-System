<script>
  import Header from "./lib/header.svelte";
  import Body from "./lib/body.svelte";
  import { DB } from "./db.js";
  import Path from "./lib/path.svelte";

  let tree = $state(
    DB.get("fileTree") || { name: "root", permission:"rw", children: [], files: [] }
  );

  let path = $state(["~"]);

  let currentDirectory = $derived(getCurrentDirectory);



  function getCurrentDirectory() {
    let current = tree;
    for (let i = 1; i < path.length; i++) {
      current = current.children.find((folder) => folder.name === path[i]);
      if (!current) {
        break;
      }
    }
    return current || tree;
  }

  function updateTree(newTree) {
    tree = newTree;
    DB.set("fileTree", tree);
  }

  function handleNewFolder() {
    const currentDir = getCurrentDirectory(); // Get the current directory based on the path

    if(currentDir.permission === "r"){
      alert("You don't have permission to create a folder in this directory");
      return;
    }

    const folderName = prompt("Enter folder name:").trim();
    if (
      folderName &&
      !currentDir.children.find((folder) => folder.name === folderName)
    ) {
      currentDir.children.push({ name: folderName, permission: currentDir.permission ,children: [], files: [] });
      updateTree(tree); // Save the updated tree
    } else {
      alert("Invalid or duplicate folder name");
    }
  }

  function handleNewFile() {
    const currentDir = getCurrentDirectory(); // Get the current directory based on the path

    if(currentDir.permission === "r"){
      alert("You don't have permission to create a file in this directory");
      return;
    }

    const fileName = prompt("Enter file name:").trim();
    if (fileName && !currentDir.files.find((file) => file.name === fileName)) {
      currentDir.files.push({ name: fileName, permission: currentDir.permission });
      updateTree(tree); // Save the updated tree
    } else {
      alert("Invalid or duplicate file name");
    }
  }

  function goBack() {
    if (path.length > 1) {
      path.pop();
    }
  }

  function handleDelete() {
    alert("Item deleted");
    // Logic to handle deletion
  }

  function handleRename() {
    alert("Item renamed");
    // Logic to handle renaming
  }

  function handlePermissions() {
    alert("Permissions changed");
    // Logic to handle permissions change
  }

  function handleSearch() {
    let keyword = prompt("Enter search keyword:").trim();
  }

  let refetchTree = () => {
    tree = DB.get("fileTree") || { name: "root", children: [], files: [] };
  };
</script>

<main class="bg-slate-800 h-screen">

  <Header
    {path}
    on:newFolder={handleNewFolder}
    on:search={handleSearch}
    on:back={goBack}
    on:newFile={handleNewFile}
  />

  <Path {path} />

  <Body tree={currentDirectory()} {path} updated={refetchTree} />
</main>

<style>
  /* Add your styles here */
</style>

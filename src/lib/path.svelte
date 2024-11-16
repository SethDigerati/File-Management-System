<script>
 let {path = []} = $props();
</script>


<div class="p-4 text-white">
  <span class="text-sm">Path: </span>
  <span class="text-lg">{path.join(' / ')}</span>
</div>
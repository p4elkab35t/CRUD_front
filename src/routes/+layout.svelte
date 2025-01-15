<script lang="ts">
    let { children } = $props();
    import "../app.css";
</script>
  
<main class="flex flex-col items-center justify-center min-h-screen bg-gray-50 p-4">
    {@render children()}
</main>
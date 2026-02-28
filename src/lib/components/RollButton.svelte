<script lang="ts">
  export let onClick: () => void;
</script>

<button
  class="w-full h-full bg-linear-to-br from-purple-600 via-pink-500 to-blue-500 
         hover:scale-105 active:scale-95 transition-all duration-200 
         text-white font-black text-4xl rounded-full shadow-neon uppercase italic"
  on:click={onClick}
>
  Draai!
</button>

<style>
  .shadow-neon {
    box-shadow: 0 0 20px #ff6ec7, 0 0 40px rgba(255, 110, 199, 0.5);
  }
</style>
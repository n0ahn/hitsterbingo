<script lang="ts">
  export let side: 'A' | 'B';
  export let onToggle: () => void;
</script>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<!-- svelte-ignore a11y_click_events_have_key_events -->
<div class="flex items-center cursor-pointer select-none" on:click={onToggle}>
  <span class="mr-3 text-white font-semibold">KANT A</span>
  <div class="relative w-14 h-8 bg-gray-800 rounded-full shadow-inner transition-colors duration-300">
    <div
      class="absolute top-0.5 left-0.5 w-7 h-7 bg-pink-500 rounded-full shadow-md transition-all duration-300"
      class:left-0.5={side === 'A'}
      class:left-6={side === 'B'}
    ></div>
  </div>
  <span class="ml-3 text-white font-semibold">KANT B</span>
</div>
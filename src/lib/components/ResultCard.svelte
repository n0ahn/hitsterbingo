<script lang="ts">
  import { fade, scale } from 'svelte/transition';
  export let color: string;
  export let name: string;
  export let icon: string;
</script>

<div
  class="w-full h-full rounded-3xl flex flex-col items-center justify-center shadow-2xl {color} text-white font-bold text-center p-6 border-4 border-white/20"
  transition:scale={{ duration: 400 }}
>
  <div class="text-7xl mb-4 drop-shadow-lg">{icon}</div>
  <div class="text-2xl sm:text-3xl leading-tight uppercase tracking-wide">{name}</div>
</div>
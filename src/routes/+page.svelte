<script lang="ts">
  import { fade, scale, fly } from 'svelte/transition';
  import { backOut } from 'svelte/easing';

  // --- STATE & LOGICA ---
  let side: 'A' | 'B' = 'A';
  let result: BingoOption | null = null;
  let rolling = false;
  let showFinal = false;
  let isFullscreen = false;

  interface BingoOption { color: string; name: string; icon: string; border: string }
  
  const options: Record<'A' | 'B', BingoOption[]> = {
    A: [
      { color: 'bg-emerald-500', border: 'border-emerald-400', name: 'Groep of Solo Artiest', icon: '🎤' },
      { color: 'bg-rose-500', border: 'border-rose-400', name: 'Voor het jaar 2000?', icon: '⏳' },
      { color: 'bg-amber-500', border: 'border-amber-400', name: 'Jaartal ± 4 jaar', icon: '📅' },
      { color: 'bg-fuchsia-600', border: 'border-fuchsia-500', name: 'Welk Decennium?', icon: '📼' },
      { color: 'bg-sky-500', border: 'border-sky-400', name: 'Jaartal ± 2 jaar', icon: '🎵' }
    ],
    B: [
      { color: 'bg-emerald-600', border: 'border-emerald-500', name: 'Titel van het nummer', icon: '🎶' },
      { color: 'bg-rose-600', border: 'border-rose-500', name: 'Exacte Jaartal', icon: '🕰️' },
      { color: 'bg-amber-600', border: 'border-amber-500', name: 'Naam Artiest/Groep', icon: '👤' },
      { color: 'bg-fuchsia-700', border: 'border-fuchsia-600', name: 'Welk Decennium?', icon: '📼' },
      { color: 'bg-sky-600', border: 'border-sky-500', name: 'Jaartal ± 3 jaar', icon: '📅' }
    ]
  };

  const toggleSide = () => {
    if (rolling) return;
    side = side === 'A' ? 'B' : 'A';
    reset();
  };

  const reset = () => {
    result = null;
    rolling = false;
    showFinal = false;
  };

  const getRandom = () => options[side][Math.floor(Math.random() * options[side].length)];

  async function roll() {
    if (rolling || showFinal) return;
    rolling = true;
    result = getRandom();

    for (let i = 0; i < 15; i++) {
      result = getRandom();
      const delay = 50 + (i * i * 1.2); 
      await new Promise(r => setTimeout(r, delay));
    }

    rolling = false;
    showFinal = true;
  }

  // Fullscreen Logica
  function toggleFullscreen() {
    if (!document.fullscreenElement) {
      document.documentElement.requestFullscreen().catch(err => {
        console.error(`Error attempting to enable full-screen mode: ${err.message}`);
      });
      isFullscreen = true;
    } else {
      document.exitFullscreen();
      isFullscreen = false;
    }
  }

  // Toetsenbord afhandeling
  function handleKeydown(event: KeyboardEvent) {
    if (event.key === ' ' || event.key === 'Enter') {
      event.preventDefault(); 
      if (showFinal) {
        reset();
      } else if (!rolling) {
        roll();
      }
    }
    if (event.key.toLowerCase() === 's' || event.key === 'Tab') {
      event.preventDefault();
      toggleSide();
    }
    if (event.key.toLowerCase() === 'f') {
      toggleFullscreen();
    }
  }
</script>

<svelte:window on:keydown={handleKeydown} />

<div class="min-h-screen bg-[radial-gradient(circle_at_top,var(--tw-gradient-stops))] from-gray-800 via-gray-950 to-black text-white flex flex-col items-center p-6 sm:p-10 overflow-hidden font-sans select-none">
  
  <header class="w-full max-w-7xl flex justify-between items-center mb-16 px-4 py-6 border-b border-white/5 backdrop-blur-sm">
    <div class="flex flex-col w-50">
        <h1 class="text-3xl sm:text-4xl font-black tracking-tighter italic uppercase text-transparent bg-clip-text bg-linear-to-r from-pink-500 via-purple-500 to-indigo-500 leading-none">
            Hitster
        </h1>
        <span class="text-[10px] tracking-[0.4em] uppercase font-bold text-gray-500">Bingo Edition</span>
    </div>

    <button on:click={toggleSide} class="group flex items-center bg-white/5 hover:bg-white/10 p-1.5 rounded-full transition-all duration-300 border border-white/10 shadow-xl">
        <span class="px-4 text-xs font-black tracking-widest transition-opacity {side === 'A' ? 'text-pink-500' : 'text-gray-500 opacity-50'}">KANT A</span>
        <div class="relative w-14 h-8 bg-gray-900 rounded-full border border-white/10">
            <div class="absolute top-1 transition-all duration-500 ease-out w-5.5 h-5.5 rounded-full shadow-[0_0_15px_rgba(236,72,153,0.5)] {side === 'A' ? 'left-1 bg-pink-500' : 'left-7 bg-indigo-500 shadow-[0_0_15px_rgba(99,102,241,0.5)]'}"></div>
        </div>
        <span class="px-4 text-xs font-black tracking-widest transition-opacity {side === 'B' ? 'text-indigo-400' : 'text-gray-500 opacity-50'}">KANT B</span>
    </button>
  </header>

  <main class="flex-1 flex items-center justify-center w-full max-w-lg relative">
    
    {#if !rolling && !showFinal}
      <div out:fade={{ duration: 0 }} in:scale={{ duration: 600, easing: backOut }} class="relative group">
        <div class="absolute inset-0 bg-linear-to-r from-pink-600 to-indigo-600 rounded-full blur-3xl opacity-20 group-hover:opacity-40 transition-opacity duration-500 animate-pulse"></div>
        
        <button 
            on:click={roll}
            class="relative w-64 h-64 bg-white/5 border border-white/10 rounded-full flex items-center justify-center text-4xl font-black uppercase tracking-tighter hover:scale-105 active:scale-95 transition-all duration-300 shadow-2xl backdrop-blur-xl overflow-hidden focus:outline-none focus:ring-2 focus:ring-pink-500/50"
        >
            <span class="z-10 bg-clip-text text-transparent bg-linear-to-b from-white to-gray-400">Draai</span>
            <div class="absolute inset-0 bg-linear-to-tr from-white/10 to-transparent"></div>
        </button>
        <p class="absolute -bottom-12 left-0 right-0 text-center text-gray-600 text-[10px] tracking-widest uppercase font-bold">Druk op Spatie of Enter</p>
      </div>
    {/if}

    {#if rolling && result}
      <div class="w-full px-4" in:fade={{ duration: 200 }}>
        <div class="aspect-[1.6/1] w-full bg-white/5 rounded-4xl p-1 shadow-inner border border-white/5 overflow-hidden">
            <div class="w-full h-full {result.color} {result.border} border-4 rounded-[1.8rem] flex flex-col items-center justify-center shadow-2xl transition-colors duration-200">
                <span class="text-8xl mb-4 drop-shadow-2xl animate-bounce">{result.icon}</span>
                <span class="text-2xl font-black uppercase tracking-tight text-white/90">{result.name}</span>
            </div>
        </div>
        <div class="mt-10 flex flex-col items-center">
            <div class="h-1 w-32 bg-white/10 rounded-full overflow-hidden">
                <div class="h-full bg-pink-500 animate-loading-bar"></div>
            </div>
            <p class="mt-4 text-[10px] font-black tracking-[0.5em] text-pink-500 uppercase animate-pulse">Kiezen...</p>
        </div>
      </div>
    {/if}
  </main>

  {#if showFinal && result}
    <div class="fixed inset-0 flex flex-col items-center justify-center z-50 p-6" transition:fade={{duration: 300}}>
      <!-- svelte-ignore a11y_click_events_have_key_events -->
      <!-- svelte-ignore a11y_no_static_element_interactions -->
      <div class="absolute inset-0 bg-black/85 backdrop-blur-xl" on:click={reset}></div>
      
      <div transition:fly={{ y: 50, duration: 600, easing: backOut }} class="relative z-10 w-full max-w-md">
        <div class="text-center mb-8">
            <h2 class="text-sm font-black tracking-[0.4em] text-pink-500 uppercase mb-2 italic">Opdracht</h2>
            <div class="h-px w-12 bg-pink-500 mx-auto opacity-50"></div>
        </div>

        <div class="aspect-[1.6/1] w-full rounded-[2.5rem] {result.color} border-[6px] {result.border} shadow-[0_0_100px_rgba(0,0,0,0.6)] flex flex-col items-center justify-center p-8 relative overflow-hidden group">
            <div class="absolute inset-0 opacity-10 bg-[url('https://www.transparenttextures.com/patterns/carbon-fibre.png')]"></div>
            <span class="text-9xl mb-6 z-10 drop-shadow-2xl">{result.icon}</span>
            <h3 class="text-3xl font-black uppercase tracking-tighter text-center leading-none z-10 drop-shadow-md">
                {result.name}
            </h3>
        </div>

        <button 
          on:click={reset}
          class="mt-16 w-full py-5 bg-white text-black font-black rounded-2xl hover:bg-pink-500 hover:text-white transition-all transform hover:-translate-y-1 active:scale-95 uppercase tracking-widest text-sm shadow-2xl focus:outline-none focus:ring-4 focus:ring-white/20"
        >
          Volgende Speler (Enter)
        </button>
      </div>
    </div>
  {/if}

  <button 
    on:click={toggleFullscreen}
    class="fixed bottom-6 right-6 p-4 bg-white/5 border border-white/10 rounded-2xl backdrop-blur-md hover:bg-white/10 transition-all active:scale-90 z-60 text-gray-400 hover:text-white"
    title="Toggle Fullscreen (F)"
  >
    {#if isFullscreen}
      <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M8 3v3a2 2 0 0 1-2 2H3m18 0h-3a2 2 0 0 1-2-2V3m0 18v-3a2 2 0 0 1 2-2h3M3 16h3a2 2 0 0 1 2 2v3"/></svg>
    {:else}
      <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M15 3h6v6M9 21H3v-6M21 3l-7 7M3 21l7-7"/></svg>
    {/if}
  </button>
</div>

<style>
  :global(body) {
    background-color: #030712;
    margin: 0;
  }

  @keyframes loading-bar {
    0% { width: 0%; transform: translateX(-100%); }
    50% { width: 100%; transform: translateX(0%); }
    100% { width: 0%; transform: translateX(100%); }
  }

  .animate-loading-bar {
    animation: loading-bar 1.5s infinite ease-in-out;
  }
</style>
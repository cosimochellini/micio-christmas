<script lang="ts">
  import { onMount } from "svelte";

  let isPlaying = false;
  let currentTime = 0;
  let intervalId;
  let progress = 0;
  let duration = 62000; // Total duration in ms

  const lyric = [
    // [Mysterious Intro]
    [
      {
        text: "Oh micio, streghetta magica, regina dei poteri,",
        time: { start: 0, end: 2000 },
        type: "intro",
      },
      {
        text: "Con te ogni giorno è pieno di assurdi misteri.",
        time: { start: 2000, end: 4000 },
        type: "intro",
      },
    ],
    // [Verse 1]
    [
      {
        text: "Il tuo sguardo inganna ogni prassi e ragione,",
        time: { start: 4000, end: 6000 },
        type: "verse",
      },
      {
        text: "Micio streghetta, regina surreale d'ogni strana visione.",
        time: { start: 6000, end: 8000 },
        type: "verse",
      },
      {
        text: "Tra cocktail arcani e ingredienti un po' strani,",
        time: { start: 8000, end: 10000 },
        type: "verse",
      },
      {
        text: "Cucini banchetti degni di re e di sovrani.",
        time: { start: 10000, end: 12000 },
        type: "verse",
      },
      {
        text: "Ti piace torturar applicazioni e scoprir difetti,",
        time: { start: 12000, end: 14000 },
        type: "verse",
      },
      {
        text: "Così gli sviluppatori piangono come cosetti.",
        time: { start: 14000, end: 16000 },
        type: "verse",
      },
    ],
    // [Chorus]
    [
      {
        text: "Miao miao micio strega, regina dei poteri,",
        time: { start: 16000, end: 18000 },
        type: "chorus",
      },
      {
        text: "Tra cibi scaduti e sapor di magia.",
        time: { start: 18000, end: 20000 },
        type: "chorus",
      },
      {
        text: "Un pulcino ti ama con lieve follia,",
        time: { start: 20000, end: 22000 },
        type: "chorus",
      },
      {
        text: "Con te ogni giorno è pieno di assurdi misteri.",
        time: { start: 22000, end: 24000 },
        type: "chorus",
      },
    ],
    // [Verse 2]
    [
      {
        text: "Ad un cosetto risvegli, la curiosità come un bambino,",
        time: { start: 24000, end: 26000 },
        type: "verse",
      },
      {
        text: "Ma tu dormi quasi sempre, ronfando dolce come un violino.",
        time: { start: 26000, end: 28000 },
        type: "verse",
      },
      {
        text: "Quando dormi sotto la luna, russi forte come un tuono,",
        time: { start: 28000, end: 30000 },
        type: "verse",
      },
      {
        text: "Sogni mondi incantati, dove domini il tuo trono.",
        time: { start: 30000, end: 32000 },
        type: "verse",
      },
      {
        text: "Vivi col pulcino, animaletto da sfamare,",
        time: { start: 32000, end: 34000 },
        type: "verse",
      },
      {
        text: "Speriamo per lui che tu non lo voglia sgranocchiare.",
        time: { start: 34000, end: 36000 },
        type: "verse",
      },
    ],
    // [Chorus]
    [
      {
        text: "Miao micio miao strega, regina dei poteri,",
        time: { start: 36000, end: 38000 },
        type: "chorus",
      },
      {
        text: "Tra cibi scaduti e sapor di magia.",
        time: { start: 38000, end: 40000 },
        type: "chorus",
      },
      {
        text: "Un pulcino ti ama con lieve follia,",
        time: { start: 40000, end: 42000 },
        type: "chorus",
      },
      {
        text: "Con te ogni giorno è pieno di assurdi misteri.",
        time: { start: 42000, end: 44000 },
        type: "chorus",
      },
    ],
    // [Bridge]
    [
      {
        text: "Ma dormi per ore, morbida su un cuscino,",
        time: { start: 44000, end: 46000 },
        type: "bridge",
      },
      {
        text: "Ronfi serena, sognando un pulcino.",
        time: { start: 46000, end: 48000 },
        type: "bridge",
      },
      {
        text: "La storia continua, tra palline e ronfate,",
        time: { start: 48000, end: 50000 },
        type: "bridge",
      },
      {
        text: "Micio e pulcino, creature incantate! Miao micio… MIAO!",
        time: { start: 50000, end: 52000 },
        type: "bridge",
      },
    ],
    // [Final Chorus]
    [
      {
        text: "Miao miao micio strega, regina dei poteri,",
        time: { start: 52000, end: 54000 },
        type: "chorus",
      },
      {
        text: "Tra cibi scaduti e sapor di magia.",
        time: { start: 54000, end: 56000 },
        type: "chorus",
      },
      {
        text: "Un pulcino ti ama con lieve follia,",
        time: { start: 56000, end: 58000 },
        type: "chorus",
      },
      {
        text: "Con te ogni giorno è pieno di assurdi misteri.",
        time: { start: 58000, end: 60000 },
        type: "chorus",
      },
    ],
    // [Tail]
    [
      {
        text: "Miao micio… MIAO!",
        time: { start: 60000, end: 62000 },
        type: "tail",
      },
    ],
  ];

  $: progress = (currentTime / duration) * 100;

  function togglePlay() {
    isPlaying = !isPlaying;

    if (isPlaying) {
      intervalId = setInterval(() => {
        currentTime += 10; // Increment by 10ms
      }, 10);
    } else {
      clearInterval(intervalId);
    }
  }

  let lyricsContainer;

  // Add this function to handle auto-scrolling
  function scrollToActiveLyric() {
    if (!lyricsContainer) return;

    const activeLyric = lyricsContainer.querySelector("p.active");
    if (!activeLyric) return;

    activeLyric.scrollIntoView({ behavior: "smooth", block: "nearest" });
  }

  // Watch currentTime changes to trigger scroll
  $: {
    currentTime;
    scrollToActiveLyric();
  }

  onMount(() => {
    return () => {
      if (intervalId) clearInterval(intervalId);
    };
  });
</script>

<div class="player">
  <h1 class="title">Micio Streghetta</h1>

  <div class="progress-bar">
    <div class="progress-background">
      <div class="progress-foreground" style="width: {progress}%" />
    </div>
    <span class="time current"
      >{Math.floor(currentTime / 1000)}:{(currentTime % 1000)
        .toString()
        .padStart(3, "0")}</span
    >
    <span class="time duration">1:02:000</span>
  </div>

  <div class="controls">
    <button class="control-button play-button" on:click={togglePlay}>
      {#if isPlaying}
        <svg viewBox="0 0 24 24">
          <path d="M6 19h4V5H6v14zm8-14v14h4V5h-4z" />
        </svg>
      {:else}
        <svg viewBox="0 0 24 24"><path d="M8 5v14l11-7z" /></svg>
      {/if}
    </button>
  </div>

  <div class="lyrics-container" bind:this={lyricsContainer}>
    {#each lyric as paragraph}
      <div class="paragraph" class:active={isPlaying}>
        {#each paragraph as { text, time: { start, end }, type }}
          <p
            class:active={currentTime >= start && currentTime <= end}
            class={type}
          >
            {text}
          </p>
        {/each}
      </div>
    {/each}
  </div>
</div>

<style>
  .player {
    margin: 4px;
    background: linear-gradient(to bottom, #282828, #181818);
    padding: 2rem;
    border-radius: 8px;
    color: #fff;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
      Ubuntu, Cantarell, sans-serif;
  }

  .progress-bar {
    position: relative;
    margin: 1rem 0;
    padding: 10px 0;
  }

  .progress-background {
    background: rgba(255, 255, 255, 0.1);
    height: 4px;
    border-radius: 2px;
    position: relative;
    overflow: hidden;
  }

  .progress-foreground {
    background-color: #fff;
    height: 100%;
    border-radius: 2px;
    transition: width 0.1s linear;
    position: absolute;
    top: 0;
    left: 0;
  }

  .time {
    font-size: 0.75rem;
    color: #b3b3b3;
    position: absolute;
    top: 20px;
  }

  .current {
    left: 0;
  }

  .duration {
    right: 0;
  }

  .controls {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 1rem;
    margin: 2rem 0;
  }

  .control-button {
    background: none;
    border: none;
    color: #b3b3b3;
    cursor: pointer;
    padding: 0.5rem;
    border-radius: 50%;
    transition: all 0.2s ease;
  }

  .control-button:hover {
    color: #fff;
    transform: scale(1.1);
  }

  .play-button {
    background: #fff;
    width: 56px;
    height: 56px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .play-button:hover {
    background: #1db954;
    transform: scale(1.05);
  }

  .play-button svg {
    width: 24px;
    height: 24px;
    fill: #000;
  }

  .lyrics-container {
    margin-top: 2rem;
    max-height: 60vh;
    overflow-y: auto;
    -ms-overflow-style: none; /* IE and Edge */
    scrollbar-width: none; /* Firefox */
  }

  /* Hide scrollbar for Chrome, Safari and Opera */
  .lyrics-container::-webkit-scrollbar {
    display: none;
  }

  .paragraph {
    margin-bottom: 1.5rem;
  }

  p {
    margin: 0.5rem 0;
    color: #b3b3b3;
    transition: all 0.3s ease;
    font-size: 1.1rem;
    line-height: 1.5;
  }

  p.active {
    color: #fff;
    font-size: 1.2rem;
    font-weight: 500;
  }
</style>

<script>
  import { onMount } from "svelte";
  import { fade } from "svelte/transition";
  import { lyric } from "../data/lyric";

  /**
   * @type {HTMLAudioElement}
   */
  let audio;
  let isPlaying = false;
  let currentTime = 0;

  /**
   * @type {number}
   */
  let intervalId;

  // Create audio element and set up listeners
  onMount(() => {
    audio = new Audio("/Micio Streghetta.mp3");

    audio.addEventListener("timeupdate", () => {
      currentTime = audio.currentTime * 1000; // Convert to milliseconds
    });

    audio.addEventListener("loadedmetadata", () => {
      duration = audio.duration * 1000; // Convert to milliseconds
    });

    return () => {
      audio.pause();
      audio.removeEventListener("timeupdate", () => {});
      audio.removeEventListener("loadedmetadata", () => {});
    };
  });

  function togglePlay() {
    if (!audio) return;

    isPlaying = !isPlaying;
    if (isPlaying) {
      audio.play();
    } else {
      audio.pause();
    }
  }

  let lyricsContainer;

  // Add this function to handle auto-scrolling
  function scrollToActiveLyric() {
    if (!isPlaying) return;
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

  $: duration = (audio?.duration ?? 0) * 1000;

  $: progress = (currentTime / duration) * 100;
  onMount(() => {
    return () => {
      if (intervalId) clearInterval(intervalId);
    };
  });
</script>

<div class="player" transition:fade>
  <h1 class="title">Micio Streghetta</h1>
  <img src="/cover.webp" alt="Witch cat with chick" />

  <div class="progress-bar">
    <div class="progress-background">
      <div class="progress-foreground" style="width: {progress}%" />
    </div>
    <span class="time current">
      {Math.floor(currentTime / 60000)}:{Math.floor(
        (currentTime % 60000) / 1000,
      )
        .toString()
        .padStart(2, "0")}
    </span>
    <span class="time duration">
      {Math.floor(duration / 60000)}:{Math.floor((duration % 60000) / 1000)
        .toString()
        .padStart(2, "0")}
    </span>
  </div>

  <div class="controls">
    <button
      class="control-button play-button"
      class:playing={isPlaying}
      on:click={togglePlay}
    >
      {#if isPlaying}
        <svg viewBox="0 0 24 24">
          <path d="M6 19h4V5H6v14zm8-14v14h4V5h-4z" />
        </svg>
      {:else}
        <svg viewBox="0 0 24 24">
          <path d="M8 5v14l11-7z" />
        </svg>
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
    font-family:
      "Inter",
      -apple-system,
      BlinkMacSystemFont,
      "Segoe UI",
      Roboto,
      Oxygen-Sans,
      Ubuntu,
      Cantarell,
      "Helvetica Neue",
      sans-serif;
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
    transition:
      background-color 0.5s ease-in-out,
      transform 0.3s ease;
    outline: none;
    -webkit-tap-highlight-color: transparent;
  }

  .play-button:hover {
    background: #1db954;
    transform: scale(1.05);
  }

  .play-button.playing {
    background: #1db954;
  }

  .play-button svg {
    width: 24px;
    height: 24px;
    fill: #000;
    transition: transform 0.3s ease;
    transform-origin: center;
  }

  .play-button:hover svg {
    fill: #000;
    transform: scale(1.1);
  }

  .play-button.playing svg {
    fill: #000;
  }

  .play-button:focus {
    outline: none;
    box-shadow: 0 0 0 2px rgba(29, 185, 84, 0.5);
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

  .header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 1rem;
  }

  img {
    border-radius: 12px;
    cursor: pointer;
    transition: all 0.3s ease-in-out;
    object-fit: cover;
    margin-bottom: 1.5rem;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
    pointer-events: none;
    width: 300px;
    height: 300px;
    display: flex;
    margin: auto;
  }

  .title {
    margin-bottom: 1rem;
  }
</style>

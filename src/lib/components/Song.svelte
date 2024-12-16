<script>
  import { onMount } from "svelte";
  import { fade } from "svelte/transition";
  import { lyric } from "../data/lyric";
  const SONG_URL = "/Micio Streghetta.mp3";
  const COVER_URL = "/cover.webp";
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

  // Add this function to set up media session
  function setupMediaSession() {
    if ("mediaSession" in navigator) {
      navigator.mediaSession.metadata = new MediaMetadata({
        title: "Micio Streghetta",
        artist: "Cosetto",
        album: "Micio Streghetta - Single",
        artwork: [{ src: COVER_URL, sizes: "300x300", type: "image/webp" }],
      });

      navigator.mediaSession.setActionHandler("play", () => (isPlaying = true));

      navigator.mediaSession.setActionHandler(
        "pause",
        () => (isPlaying = false),
      );
    }
  }

  // Create audio element and set up listeners
  onMount(() => {
    audio = new Audio(SONG_URL);

    const updateTime = () => {
      currentTime = audio.currentTime * 1000; // Convert to milliseconds
      if ("mediaSession" in navigator) {
        navigator.mediaSession.playbackState = isPlaying ? "playing" : "paused";
      }
    };
    const loadMetadata = () => {
      duration = audio.duration * 1000; // Convert to milliseconds
      setupMediaSession();
    };

    audio.addEventListener("timeupdate", updateTime);
    audio.addEventListener("loadedmetadata", loadMetadata);

    return () => {
      audio.pause();
      audio.removeEventListener("timeupdate", updateTime);
      audio.removeEventListener("loadedmetadata", loadMetadata);
      if (intervalId) clearInterval(intervalId);
    };
  });

  /**
   * @type {HTMLElement}
   */
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

  $: {
    isPlaying ? audio?.play() : audio?.pause();
  }

  onMount(() => {
    return () => {
      if (intervalId) clearInterval(intervalId);
    };
  });
</script>

<div class="player-container">
  <div class="player player-font" transition:fade>
    <img src="/cover.webp" alt="Witch cat with chick" />

    <div class="song-info">
      <h1 class="title">Micio Streghetta</h1>
      <h2 class="artist">Cosetto</h2>
    </div>

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
        on:click={() => (isPlaying = !isPlaying)}
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
              class:passed={currentTime > end}
              class={type}
            >
              {text
                .replaceAll(/ ama /gi, " ❤️ ")
                .replaceAll(/micio/gi, "🐱")
                .replaceAll(/pulcino/gi, "🐤")
                .replaceAll(/cosetto/gi, "🐤")
                .replaceAll(/streghetta/gi, "🧙")}
            </p>
          {/each}
        </div>
      {/each}
    </div>
  </div>
</div>

<style>
  .player-font {
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

  .player-container {
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .player {
    margin: 8px;
    max-width: 500px;
    background: linear-gradient(to bottom, #282828, #181818);
    padding: 2rem;
    border-radius: 8px;
    color: #fff;
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
    scrollbar-width: none;
  }

  .lyrics-container::-webkit-scrollbar {
    display: none;
  }

  .paragraph {
    margin-bottom: 1.5rem;
  }

  p {
    margin: 0.5rem 0;
    color: #b3b3b3;
    font-size: 1.1rem;
    line-height: 1.5;
    filter: blur(5px);
    opacity: 0.7;
    transform: scale(0.98);
    transition:
      filter 0.8s cubic-bezier(0.4, 0, 0.2, 1),
      opacity 0.8s cubic-bezier(0.4, 0, 0.2, 1),
      transform 0.8s cubic-bezier(0.4, 0, 0.2, 1),
      color 0.3s ease,
      font-size 0.3s ease;
  }

  p.active,
  p.passed {
    filter: blur(0);
    opacity: 1;
    transform: scale(1);
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

  .song-info {
    margin: 1rem 0;
  }

  .title {
    margin: 0;
    font-size: 1.5rem;
    font-weight: 700;
  }

  .artist {
    margin: 0.5rem 0 0 0;
    font-size: 1rem;
    color: #b3b3b3;
    font-weight: 500;
  }
</style>

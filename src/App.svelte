<script lang="ts">
  import { fade } from "svelte/transition";
  import cards from "../cards.json";
  import Card from "./lib/components/CardProxy.svelte";
  import Song from "./lib/components/Song.svelte";

  const showcase = cards[0];

  let cardClicked = false;
</script>

<main>
  <header class="padding">
    <h1 class="title">Micio Streghetta 🐱🌟</h1>
  </header>

  <div class="showcase padding">
    {#if !showcase}
      loading...
    {:else}
      <Card
        id={showcase.id}
        name={showcase.name}
        set={showcase.set}
        types={showcase.types}
        supertype={showcase.supertype}
        subtypes={showcase.subtypes}
        rarity={showcase.rarity}
        showcase={true}
        img={showcase.images.large}
        on:click={() => setTimeout(() => (cardClicked = true), 1000)}
      />
    {/if}
  </div>

  <section class="info">
    {#if cardClicked}
      <h2>Scroll down to a little bit...</h2>
    {:else}
      <h2 transition:fade>Click on a Card to take a Closer look!</h2>
    {/if}
  </section>

  {#if cardClicked}
    <div transition:fade>
      <Song />
    </div>
  {/if}

  <footer>
    <code class="footer">Made with ❤️ by the 🐤 for the 🐱 fried of you</code>
  </footer>
</main>

<style>
  .title {
    text-align: center;
    font-size: 1.8rem;
  }

  .footer {
    padding: 4rem 2rem;
    text-wrap: pretty;
    width: 100%;
    display: flex;
    text-align: center;
  }

  .info {
    text-align: center;
    padding-bottom: 100px;
    margin: 0 2rem;
    text-wrap: pretty;
  }

  .padding {
    padding: 30px;
  }
</style>

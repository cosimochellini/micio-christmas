<script lang="ts">
  import { onMount } from "svelte";

  import Card from "./lib/components/CardProxy.svelte";

  let showcase;

  let isLoading = true;

  const getCards = () => import("../cards.json");

  const loadCards = async () =>
    getCards().then(({ default: cards }) => {
      showcase = cards[0];
      isLoading = false;
    });

  onMount(() => {
    loadCards();
    const $headings = document.querySelectorAll("h1,h2,h3");
    const $anchor = [...$headings].filter((el) => {
      const id = el.getAttribute("id")?.replace(/^.*?-/g, "");
      const hash = window.location.hash?.replace(/^.*?-/g, "");
      return id === hash;
    })[0];
    if ($anchor) {
      setTimeout(() => {
        $anchor.scrollIntoView();
      }, 100);
    }
  });
</script>

<main>
  <header>
    <h1 id="⚓-top">Micio Streghetta</h1>

    <section class="intro" id="⚓-intro">
      <p></p>
    </section>

    <div class="showcase">
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
          isReverse={showcase.isReverse}
          showcase={true}
          img={showcase.images.large}
        />
      {/if}
    </div>

    <section class="info">
      <h2>Click on a Card to take a Closer look!</h2>
    </section>
  </header>
</main>

<div class="back-to-top">
  <a href="#⚓-top">Back to Top</a>
</div>

<style>
  .back-to-top a {
    color: inherit;
    text-decoration: none;
    z-index: 999;
  }
</style>

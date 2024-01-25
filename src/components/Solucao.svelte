<script>
  import Block from "$components/Block.svelte";
  import ImageRaw from "$components/ImageRaw.svelte";
  import inView from "$actions/inView.js";
  import { onMount } from "svelte";
  import { selectAll, easeLinear } from "d3";
  import ImageCompare from "svelte-image-compare";

  let visiblePhoto = false;
  let photos;

  function showPhotos() {
    photos
      .transition()
      .delay((d, i) => i * 250)
      .duration(500)
      .ease(easeLinear)
      .style("opacity", 1);
  }

  onMount(() => {
    photos = selectAll(".trip-wrapper .border-2");
  });

  export let text;
</script>

<div class="trip-wrapper" use:inView on:enter={() => showPhotos()}>
  <div class="relative z-0 flex flex-col justify-between w-full max-w-5xl mx-auto my-0">
    <div class="border-2 border-black" id="madera-triptych">
      <ImageCompare before="assets/img/f02_antes.jpg" after="assets/img/f02_reduzida_r01.jpg"
      ></ImageCompare>
      <!-- <p>texto 1 aqui</p> -->
    </div>

    <div class="border-2 border-black" id="gardena1-triptych">
      <ImageCompare before="assets/img/f03_antes.jpg" after="assets/img/f03_reduzida_r01.jpg"
      ></ImageCompare>
      <!-- <p>texto 2 aqui</p> -->
    </div>

    <div class="border-2 border-black" id="gardena2-triptych">
      <ImageCompare before="assets/img/f04_antes.jpg" after="assets/img/f04_reduzida_r01.jpg"
      ></ImageCompare>
      <!-- <p>texto 3 aqui</p> -->
    </div>
  </div>
</div>

<style>
  .trip-wrapper {
    height: calc(100vw * 1.25);
    padding: 1rem 1rem 0rem 1rem;
    /* max-width: 60rem; */
    max-height: 100rem;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
  }

  .trip-wrapper .border-2 {
    opacity: 0;
  }

  .relative {
    position: relative;
    height: 100%;
    width: 100%;
  }

  .text-label {
    font-family: var(--dubois);
    text-transform: uppercase;
    color: var(--color-off-white);
    font-size: 14px;
    margin-bottom: 4rem;
  }

  .border-2 {
    position: absolute;
  }

  #madera-triptych {
    text-align: center;
    top: 0;
    left: 0;
  }

  #gardena1-triptych {
    text-align: center;
    top: 50%;
    right: 0;
  }

  #gardena2-triptych {
    text-align: center;
    top: 100%;
    right: 0;
  }

  #madera-triptych,
  #gardena1-triptych,
  #gardena2-triptych {
    width: 100%;
  }

  @media only screen and (min-width: 700px) {
    .trip-wrapper {
      height: calc(100vw * 1.25);
      padding: 2rem 2rem 0rem 2rem;
    }

    #madera-triptych,
    #gardena1-triptych,
    #gardena2-triptych {
      width: 100% !important;
    }
  }

  @media only screen and (min-width: 900px) {
    .trip-wrapper {
      height: calc(100vw * 1.25);
    }

    #madera-triptych,
    #gardena1-triptych,
    #gardena2-triptych {
      width: 55%;
    }
  }
</style>

<script>
  import { extent, geoAlbers, geoPath, interpolateBrBG, scaleDiverging, scalePow } from "d3";

  import { onMount } from "svelte";
  import { feature } from "topojson-client";
  import * as us from "us";

  import Block from "$components/Block.svelte";

  import migrationData from "$data/great-migration-places-topo.json";

  import usData from "$data/geo/us-topo.json";
  import citiesData from "$data/geo/cities-geo.json";

  let states = [];
  let cities = [];
  let spikes = [];
  let domain = [];
  let land;
  let svg;
  let svgLegend;
  let rampColor;
  let x;

  let db_colors = ["#EFAE38", "#e7e7e7", "#5367A2"];

  // default
  let field = "black_pop_pct_chg_1910_1940";

  const fieldMap = {
    "1910-1940": "black_pop_pct_chg_1910_1940",
    "1940-1970": "black_pop_pct_chg_1940_1970"
  };

  /**
   *
   * @param {object} d
   * @param {string} key
   */
  const get = (d, key) => d.properties[key];

  /**
   *
   * @param {object} d
   * @param {string} key
   */
  const generateSpike = (d, key) => {
    const MAX_SPIKE_HEIGHT = 180;
    const keys = Object.values(fieldMap);

    // Array [ -0.246, 0.436 ]
    const domain = extent(Array(keys.map((k) => spikes.map((d) => d.properties[k]))).flat(2));

    const spikeScale = scalePow().exponent(2).domain(domain).range([0, MAX_SPIKE_HEIGHT]);

    const mountain = (d, mx = 5) => `M${-mx / 2} 0 Q0 ${-spikeScale(d)} ${mx / 2} 0`;

    return mountain(d.properties[key]);
  };

  /**
   *
   * @param {object} d
   * @param {string} key
   * @param {boolean} rotate
   */
  const translate = (d, key = field, rotate = true) => {
    const transform = `translate(${projection(d.geometry.coordinates)})`;
    const value = get(d, key);

    if (value >= 0 || !rotate) {
      return transform;
    } else {
      return `${transform} rotate(180)`;
    }
  };

  /**
   * @param {object} d
   */
  let colorScale = (d) => "";

  let t = {
    url() {
      return "";
    }
  };
  const projection = geoAlbers();
  const path = geoPath().projection(projection);

  function ramp(color, n = 256) {
    const canvas = document.createElement("canvas");
    canvas.width = n;
    canvas.height = 1;
    const context = canvas.getContext("2d");
    for (let i = 0; i < n; ++i) {
      context.fillStyle = color(i / (n - 1));
      context.fillRect(i, 0, 1, 1);
    }
    return canvas;
  }

  onMount(async () => {
    /**
     * Data prep
     */

    land = feature(usData, usData.objects.land);
    cities = citiesData.features;
    spikes = feature(migrationData, migrationData.objects.points).features.filter(
      (d) => d.geometry
    );
    states = feature(usData, usData.objects.states)
      .features.map((state) => {
        const properties =
          us.STATES.find((s) => s.fips === `${state.id < 10 ? `0${state.id}` : state.id}`) ?? {};
        return {
          ...state,
          properties: {
            ...properties
          }
        };
      })
      .filter((d) => d.properties?.name !== undefined);

    domain = extent(spikes.map((d) => d.properties[field]));

    colorScale = scaleDiverging().domain([domain[0], 0, domain[1]]).range(db_colors);
    rampColor = colorScale.interpolator();
  });

  const handleOnClick = (event) => {
    const key = event.target.textContent;
    field = fieldMap[key];
  };

  export let header;
  export let text;
  export let source;
</script>

<div class="w-full mx-auto flex justify-center my-12">
  <div class="w-full max-w-7xl">
    <!-- <Block> -->
      <!-- <h3 class="text-label font-bold text-xl">{@html header}</h3> -->
      <!-- <p class="text-label text-base mb-4">{@html text}</p> -->

      <!-- <span class="relative z-0 inline-flex shadow-sm rounded-md">
        <button
          on:click={handleOnClick}
          type="button"
          class="text-label relative inline-flex items-center px-4 py-2 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 active:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          >1910-1940</button
        >
        <button
          on:click={handleOnClick}
          type="button"
          class="-ml-px text-label relative inline-flex items-center px-4 py-2 rounded-r-md border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 active:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          >1940-1970</button
        >
      </span> -->
    <!-- </Block> -->

   

    <!-- <Block>
      <p class="text-label dubois-14"><strong>Source:</strong> {@html source}</p>
    </Block> -->
  </div>
</div>

<style>
  .legend {
    overflow: visible;
    display: block;
  }

  :global(#legend svg) {
    outline: 1px solid var(--color-off-black);
  }

  #legend {
    max-width: 500px;
    margin: 2rem auto;
  }

  .text-label {
    font-family: var(--dubois);
    text-transform: uppercase;
  }

  #cities .text-label {
    font-size: 24px;
    font-family: var(--dubois);
    text-transform: uppercase;
    font-weight: 700;
    text-shadow: -2px -2px #e7e7e7, 2px -2px #e7e7e7, -2px 2px #e7e7e7, 2px 2px #e7e7e7;
  }

  circle {
    stroke: #e7e7e7;
  }

  .dubois-14 {
    font-size: 14px;
    font-family: var(--dubois);
    text-transform: uppercase;
  }

  h3.text-label {
    text-align: center;
    font-size: 24px;
    font-family: var(--dubois-wide);
    padding: 0 0 1rem 0;
  }

  .text-base {
    text-align: center;
    font-size: 20px;
  }

  :global(a) {
    color: var(--color-off-black);
  }

  :global(a:hover) {
    color: var(--color-db-red);
  }

  #Philadelphia-group, #SanDiego-group, #Detroit-group, #NewOrleans-group {
      opacity: 0;
    }

  @media only screen and (min-width: 500px) {
    #cities .text-label {
      font-size: 18px;
    }

    #Detroit-group, #NewOrleans-group {
      opacity: 1;
    }
  }

  @media only screen and (min-width: 700px) {
    #cities .text-label {
      font-size: 14px;
    }

    #Philadelphia-group, #SanDiego-group, #Detroit-group, #NewOrleans-group {
      opacity: 1;
    }
  }

  @media only screen and (min-width: 800px) {
    #cities .text-label {
      font-size: 12px;
    }
  }

  @media only screen and (min-width: 1000px) {
    #cities .text-label {
      font-size: 10px;
    }
  }
</style>

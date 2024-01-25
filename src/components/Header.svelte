<script>
  import logoPrefeitura from '$svg/capa_prefeitura.png'; 
  import logoPretoHorizontal from '$svg/logo_preto_horizontal.png'; 
</script>

<header>
  <div class="headerWrapper">
  <div class="wordmark">
    <a href="https://dados.rio/" target="_blank" aria-label="The Pudding">
      <img src={logoPretoHorizontal} alt="Logo Prefeitura" />
    </a>
  </div>

  <!-- <div class="topLeftImage">
    <a href="https://prefeitura.rio/" aria-label="The Pudding">
      <img src={logoPretoHorizontal} alt="Logo Prefeitura" />
    </a>
  </div> -->
  </div>
</header>

<style>
  .headerWrapper {
 position: relative;
}
.topLeftImage {
  max-width: 12em;
  margin: 0 auto;
 position: absolute;
 padding: 1em 1em;
 top: 0;
}

  .wordmark {
    max-width: 13em;
    margin: 0 auto;
  
    padding: 1em 0;
  }

  .wordmark a {
    border: none;
    color: var(--color-fg);
  }

  .wordmark a:hover {
    background-color: transparent;
  }

  :global(.wordmark svg path) {
    fill: var(--color-off-black);
    stroke: none;
  }
  @media only screen and (max-width: 600px) {
    .topLeftImage{
      display: none;
    }
  }
</style>

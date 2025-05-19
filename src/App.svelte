<script>
  import * as d3 from "d3"
  import atletas from "/src/data/athletes.csv"
  import personajes from "/src/data/personajes.json"
  import { onMount } from 'svelte';
  // import atletas from "/src/data/athletes.json"

  console.log("atletas", atletas)

  const imagenCasas = {
    Gryffindor: "/public/images/gryffindor.svg",
    Hufflepuff: "/public/images/hufflepuff.svg",
    Ravenclaw: "/public/images/ravenclaw.svg",
    Slytherin: "/public/images/slytherin.svg",
  }

  const imagenGenero = {
    HombrePri: "/public/images/chico_pri.svg",
    HombreSecu: "/public/images/chico_secu.svg",
    MujerPri: "/public/images/chica_pri.svg",
    MujerSecu: "/public/images/chica_secu.svg",
  }

  const imagenMascotas = {
    Gato: "/public/images/gato.svg",
    Lechuza: "/public/images/lechuza.svg",
    Sapo: "/public/images/sapo.svg",
    Rata: "/public/images/rata.svg",
    Phoenix: "/public/images/phoenix.svg",
  }

  const imagenBando = "/public/images/sombrero.svg";

  const imagenMagia = "/public/images/brillo.svg";

  const imagenLibros = {
    1: "/public/images/libro_uno.svg",
    2: "/public/images/libro_dos.svg",
    3: "/public/images/libro_tres.svg",
    4: "/public/images/libro_cuatro.svg",
    5: "/public/images/libro_cinco.svg",
    6: "/public/images/libro_seis.svg",
    7: "/public/images/libro_siete.svg", 
  }

  /* 1. Escala para participaciones (cuantitativo > grosor) */
  const minMaxParticipations = d3.extent(atletas, (d) => d.participations)
  let grosorPartic = d3.scaleLinear()
    .domain(minMaxParticipations).range([2, 18])

  /* 2. Escala para medallas (cuantitativo > diámetro círculo) */
  const maxMedallas = d3.max(atletas, (d) => d.medallas)
  const diamMedallas = d3.scaleRadial()
    .domain([0, maxMedallas]).range([0, 100])

  /* 3. Escala para genero (categórico > color) */
  const colorGenero = d3.scaleOrdinal()
    .domain(["F", "M"])
    .range(["#F7DDBA", "#E4D9F2"])

  /* 4. Escala para continentes (categórico > color)   */
  const colorContinentes = d3
    .scaleOrdinal()
    .domain(["América", "África", "Asia", "Europa", "Oceanía"])
    .range(["#ed334e", "#000000", "#fbb132", "#009fe3", "#00963f"])

</script>

<main>
  <div class="header">
    <img src="/images/logo_referencias.svg" width="190" alt="anillos" />
    <h3 class="headline">
      <b>Los reyes del oro</b>
      Medallas, participaciones y dominio en distintos continentes
    </h3>
    <p class="bajada">
      Los atletas con más medallas olímpicas de oro en los Juegos Olímpicos
    </p>
    <img
      class="referencias"
      src="/images/referencias.svg"
      width="490"
      alt="anillos"
    />
  </div>

    <!-- Conedor de las entidades -->
    <div class="container">
      
      <!-- Iteramos la data para visualizar c/ entidad -->
      {#each atletas as atleta}
        <div class="person-container">
          <div
            class="person"
            style="
              border-color: {colorContinentes(atleta.continent)};
              background-color:{colorGenero(atleta.gender)}; 
              width: {diamMedallas(atleta.medallas)}px; 
              height: {diamMedallas(atleta.medallas)}px; 
              border-width: {grosorPartic(atleta.participations)}px; 
            ">
          </div>
        </div>
      {/each}
      <!-- Fin iteración -->

    </div>
    <div class="catalogo-container">
        <!-- Iteramos la data para visualizar c/ entidad -->
        {#each personajes as personaje}
          <div class="personaje-container superpuesto">

            {#if personaje.genero == "Hombre"}
              {#if personaje.protagonismo == "Principal"}
                <img class="personaje" src={imagenGenero["HombrePri"]} alt="HombrePrincipal" />
              {:else}
                <img class="personaje" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
              {/if}
            {:else}
              {#if personaje.protagonismo == "Principal"}
                <img class="personaje" src={imagenGenero["MujerPri"]} alt="MujerPrincipal" />
              {:else}
                <img class="personaje" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
              {/if}
            {/if}

            <img class="casa" src={imagenCasas[personaje.casa]} alt="Casa" />
            
            {#if personaje.tiene_mascota}
              <img src={imagenMascotas[personaje.mascota]} alt="Mascota" />
            {/if}

            <img src={imagenLibros[personaje.libros]} alt="Libros" />

            <img src={imagenBando} alt="Bando" />

          </div>
        {/each}
      <!-- Fin iteración -->
    </div>

</main>


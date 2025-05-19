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

  // Separar personajes en dos grupos para las páginas del libro
  const personajesIzquierda = personajes.slice(0, 4);
  const personajesDerecha = personajes.slice(4, 8);

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

  <div class="header">
    <h1>Catálogo de Personajes de Harry Potter</h1>
  </div>

  <div class="book-container">
    <!-- Imagen del libro como fondo -->
    <img src="/public/images/librofondo.jpeg" alt="Libro abierto" class="book-background">
      <div class="book-pages">
      <!-- Página izquierda -->
      <div class="book-page left-page">
        {#each personajesIzquierda as personaje}
          <div class="personaje-container">
            <!-- Imagen de personaje según género y protagonismo -->
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

            <!-- Imagen de la casa -->
            <img class="casa" src={imagenCasas[personaje.casa]} alt="Casa" />
            
            <!-- Mascota, si tiene -->
            {#if personaje.tiene_mascota}
              <img class="mascota" src={imagenMascotas[personaje.mascota]} alt="Mascota" />
            {/if}

            <!-- Libros en los que aparece -->
            <img class="libros" src={imagenLibros[personaje.libros]} alt="Libros" />

            <!-- Indicador de bando -->
            <img class="bando" src={imagenBando} alt="Bando" />
          </div>
        {/each}
      </div>
      
      <!-- Página derecha -->
      <div class="book-page right-page">
        {#each personajesDerecha as personaje}
          <div class="personaje-container">
            <!-- Imagen de personaje según género y protagonismo -->
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

            <!-- Imagen de la casa -->
            <img class="casa" src={imagenCasas[personaje.casa]} alt="Casa" />
            
            <!-- Mascota, si tiene -->
            {#if personaje.tiene_mascota}
              <img class="mascota" src={imagenMascotas[personaje.mascota]} alt="Mascota" />
            {/if}

            <!-- Libros en los que aparece -->
            <img class="libros" src={imagenLibros[personaje.libros]} alt="Libros" />

            <!-- Indicador de bando -->
            <img class="bando" src={imagenBando} alt="Bando" />
          </div>
        {/each}
      </div>
    </div>
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
              <img class="personaje" style="left: 35px;" src={imagenGenero["MujerPri"]} alt="MujerPrincipal" />
            {:else}
              <img class="personaje" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
            {/if}
          {/if}
          

          {#if personaje.genero == "Hombre" && personaje.protagonismo == "Principal"}
            <img class="sombrero" style="left: 68px;" src={imagenBando} alt="Bando" />
            <img class="libros" style="margin-right: -180px;" src={imagenLibros[personaje.libros]} alt="Libros" />
            <img class="casa" style="left: 35px;" src={imagenCasas[personaje.casa]} alt="Casa" />
          {:else if personaje.genero == "Mujer" && personaje.protagonismo == "Principal"}
            <img class="sombrero" style="left: 53px;" src={imagenBando} alt="Bando" />
            <img class="libros" style="margin-right: -140px;" src={imagenLibros[personaje.libros]} alt="Libros" />
            <img class="casa" style="left: 23px;" src={imagenCasas[personaje.casa]} alt="Casa" />
          {:else}
            <img class="sombrero" src={imagenBando} alt="Bando" />
            <img class="libros" src={imagenLibros[personaje.libros]} alt="Libros" />
            <img class="casa" src={imagenCasas[personaje.casa]} alt="Casa" />
          {/if}

          {#if personaje.mascota == "Gato"}
            <img class="gato" src={imagenMascotas["Gato"]} alt="Gato" />
          {:else if personaje.mascota == "Lechuza"}
            <img class="lechuza" src={imagenMascotas["Lechuza"]} alt="Lechuza" />
          {:else if personaje.mascota == "Sapo"}
            <img class="sapo" src={imagenMascotas["Sapo"]} alt="Sapo" />
          {:else if personaje.mascota == "Rata"}
            <img class="rata" src={imagenMascotas["Rata"]} alt="Rata" />
          {:else}
            <img class="phoenix" src={imagenMascotas["Phoenix"]} alt="Phoenix" />
          {/if}


        </div>
      {/each}
    <!-- Fin iteración -->
  </div>

</main>


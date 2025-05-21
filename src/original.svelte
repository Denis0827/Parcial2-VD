<head>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons/font/bootstrap-icons.css" rel="stylesheet" />

</head>

<script>
  import * as d3 from "d3"
  import atletas from "/src/data/athletes.csv"
  import personajes from "/src/data/personajes.json"
  import { onMount } from 'svelte';
  // import atletas from "/src/data/athletes.json"

  console.log("atletas", atletas)

  const imagenCasas = {
    Gryffindor: "/images/gryffindor.svg",
    Hufflepuff: "/images/hufflepuff.svg",
    Ravenclaw: "/images/ravenclaw.svg",
    Slytherin: "/images/slytherin.svg",
  }

  const imagenGenero = {
    HombrePri: "/images/chico_pri.svg",
    HombreSecu: "/images/chico_secu.svg",
    MujerPri: "/images/chica_pri.svg",
    MujerSecu: "/images/chica_secu.svg",
  }

  const imagenMascotas = {
    Gato: "/images/gato.svg",
    Lechuza: "/images/lechuza.svg",
    Sapo: "/images/sapo.svg",
    Rata: "/images/rata.svg",
    Phoenix: "/images/phoenix.svg",
  }

  const imagenBando = "/images/sombrero.svg";

  const imagenMagia = "/images/brillo.svg";

  const imagenLibros = {
    1: "/images/libro_uno.svg",
    2: "/images/libro_dos.svg",
    3: "/images/libro_tres.svg",
    4: "/images/libro_cuatro.svg",
    5: "/images/libro_cinco.svg",
    6: "/images/libro_seis.svg",
    7: "/images/libro_siete.svg", 
  }

  // Separar personajes en dos grupos para las páginas del libro
  const personajesIzquierda = personajes.slice(0, 4);
  const personajesDerecha = personajes.slice(4, 8);

  let codigos = {
    codHombre: true,
    codMujer: true,
    codPrincipal: true,
    codSecundario: true,
    codVivo: true,
    codMuerto: true,
    codGryffindor: true,
    codHufflepuff: true,
    codRavenclaw: true,
    codSlytherin: true,
    codBueno: true,
    codMalo: true,
    codAlto: true,
    codMedio: true,
    codBajo: true,
    codUno: true,
    codDos: true,
    codLechuza: true,
    codGato: true,
    codSapo: true,
    codRata: true,
    codPhoenix: true,
  };

  function mostrarSolo(mostrados, noMostrados) {
    for (let key of mostrados) {
      codigos[key] = true;
    }
    for (let key of noMostrados) {
      codigos[key] = false;
    }

    setTimeout(() => {
      mostrarTodos(noMostrados);
    }, 3000);
  }

  function mostrarTodos(noMostrados) {
    for (let key of noMostrados) {
      codigos[key] = true;
    }
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

<body>
  <main>
    
    
    <div class="home">
      <nav class="navbar navbar-expand-lg navbar-light" style="background-color: var(--color_nav);">
        <div class="container-fluid">
          <a class="navbar-brand" href="#">
            <img src="/public/images/logo.png" alt="Logo" width="120px" height="24">
          </a>
          <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarSupportedContent">
            <ul class="navbar-nav me-auto mb-2 mb-lg-0">
              <li class="nav-item">
                <a class="nav-link active" aria-current="page" href="#">Home</a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="#">Representación</a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="#">Codificación</a>
              </li>
            </ul>
            <form class="d-flex">
              <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
              <button class="btn btn-outline-success" type="submit">Search</button>
            </form>
          </div>
        </div>
      </nav>

      <img src="/public/images/fondo.jpg" alt="Fondo" class="fondo" />
      
    </div>

    <div class="header color-fondo">
      <img src="/public/images/banderas.png" width="300" alt="banderas">
      <button><a href="https://www.harrypotter.com/es/sorting-hat">Descubre</a></button>
    </div>
    
    <div class="book-container">
      
      <!-- Imagen del libro como fondo -->
      <img style="filter: brightness(0.8);" src="/public/images/librofondo.jpeg" alt="Libro abierto" class="book-background">
        <div class="book-pages">
        <!-- Página izquierda -->
        <div class="catalogo-container book-page">
          {#each personajesIzquierda as personaje}
            <div>
              <div class="personaje-container superpuesto">
                {#if personaje.genero == "Hombre"}
                  {#if personaje.protagonismo == "Principal"}
                    <img class="personaje" src={imagenGenero["HombrePri"]} alt="HombrePrincipal" />
                  {:else}
                    <img class="personaje" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
                    
                  {/if}
                {:else}
                  {#if personaje.protagonismo == "Principal"}
                    <img class="personaje" style="left: 8px;" src={imagenGenero["MujerPri"]} alt="MujerPrincipal" />
                  {:else}
                    <img class="personaje" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
                  {/if}
                {/if}

                {#if personaje.genero == "Hombre" && personaje.protagonismo == "Principal"}
                  <img class="sombrero" style="left: 2.7vw; top: 3vh;" src={imagenBando} alt="Bando" />
                  <img class="libros" style="margin-right: -6vw;" src={imagenLibros[personaje.libros]} alt="Libros" />
                  <img class="casa" style="left: 1.75vw; top: 10.5vh;" src={imagenCasas[personaje.casa]} alt="Casa" />
                {:else if personaje.genero == "Mujer" && personaje.protagonismo == "Principal"}
                  <img class="sombrero" style="left: 2.4vw; top: 4.5vh;" src={imagenBando} alt="Bando" />
                  <img class="libros" style="margin-right: -5vw;" src={imagenLibros[personaje.libros]} alt="Libros" />
                  <img class="casa" style="left: 1.4vw; top: 11.5vh;" src={imagenCasas[personaje.casa]} alt="Casa" />
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
                  {#if personaje.genero == "Hombre" && personaje.protagonismo == "Principal"}
                    <img class="rata" style="top: 8.2vh;" src={imagenMascotas["Rata"]} alt="Rata" />
                  {:else}
                    <img class="rata" src={imagenMascotas["Rata"]} alt="Rata" />
                  {/if}
                {:else}
                  <img class="phoenix" src={imagenMascotas["Phoenix"]} alt="Phoenix" />
                {/if}
              </div>

              <div class="nombre-personaje">
                <p>{personaje.personaje}</p>
              </div>

            </div>
          {/each}
        </div>
        
        <!-- Página derecha -->
        <div class="catalogo-container book-page">
          {#each personajesDerecha as personaje}
            <div>
              <div class="personaje-container superpuesto">
                {#if personaje.genero == "Hombre"}
                  {#if personaje.protagonismo == "Principal"}
                    <img class="personaje" src={imagenGenero["HombrePri"]} alt="HombrePrincipal" />
                  {:else}
                    <img class="personaje" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
                    
                  {/if}
                {:else}
                  {#if personaje.protagonismo == "Principal"}
                    <img class="personaje" style="left: 8px;" src={imagenGenero["MujerPri"]} alt="MujerPrincipal" />
                  {:else}
                    <img class="personaje" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
                  {/if}
                {/if}

                {#if personaje.genero == "Hombre" && personaje.protagonismo == "Principal"}
                  <img class="sombrero" style="left: 2.7vw; top: 3vh;" src={imagenBando} alt="Bando" />
                  <img class="libros" style="margin-right: -6vw;" src={imagenLibros[personaje.libros]} alt="Libros" />
                  <img class="casa" style="left: 1.75vw; top: 10.5vh;" src={imagenCasas[personaje.casa]} alt="Casa" />
                {:else if personaje.genero == "Mujer" && personaje.protagonismo == "Principal"}
                  <img class="sombrero" style="left: 2.4vw; top: 4.5vh;" src={imagenBando} alt="Bando" />
                  <img class="libros" style="margin-right: -5vw;" src={imagenLibros[personaje.libros]} alt="Libros" />
                  <img class="casa" style="left: 1.4vw; top: 11.5vh;" src={imagenCasas[personaje.casa]} alt="Casa" />
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
                  {#if personaje.genero == "Hombre" && personaje.protagonismo == "Principal"}
                    <img class="rata" style="top: 8.2vh;" src={imagenMascotas["Rata"]} alt="Rata" />
                  {:else}
                    <img class="rata" src={imagenMascotas["Rata"]} alt="Rata" />
                  {/if}
                {:else}
                  <img class="phoenix" src={imagenMascotas["Phoenix"]} alt="Phoenix" />
                {/if}
              </div>

              <div class="nombre-personaje">
                <p>{personaje.personaje}</p>
              </div>

            </div>
          {/each}
        </div>
      </div>
    </div>

    <div class="header color-fondo">
      <h1>Codificación</h1>
    </div>

    <div class="codificacion-container color-fondo">

      <div class="cod-fila">

        <div class="cod">
          <div style="justify-content: center; display: flex;">
            {#if codigos.codHombre}
              <img style="height: 20vh;" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
            {:else}
              <img style="height: 20vh; opacity: 0.2;" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
            {/if}
            {#if codigos.codMujer}
              <img style="height: 20vh;" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
            {:else}
              <img style="height: 20vh; opacity: 0.2;" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
            {/if}
          </div>

          <div style="margin-top: 8px;">
            <p class="categoria-texto"><b>Género</b> <br> Dato categórico | Canal: Símbolo</p>
          </div>

          <div style="margin-top: -8px;" class="btn-group" role="group" aria-label="Basic outlined example">
            <button on:click={() => mostrarSolo(["codHombre"], ["codMujer"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Hombre</button>
            <button on:click={() => mostrarSolo(["codMujer"], ["codHombre"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Mujer</button>
          </div>
        </div>

        <div class="cod">
          <div class="cod-protagonismo-img">
            {#if codigos.codPrincipal}
              <img style="height: 21.5vh;" src={imagenGenero["HombrePri"]} alt="HombrePrincipal" />
              <img style="height: 22.5vh;" src={imagenGenero["MujerPri"]} alt="MujerPrincipal" />
            {:else}
              <img style="height: 21.5vh; opacity: 0.2;" src={imagenGenero["HombrePri"]} alt="HombrePrincipal" />
              <img style="height: 22.5vh; opacity: 0.2;" src={imagenGenero["MujerPri"]} alt="MujerPrincipal" />
            {/if}
            {#if codigos.codSecundario}
              <img style="height: 20vh;" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
              <img style="height: 20vh;" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
            {:else}
              <img style="height: 20vh; opacity: 0.2;" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
              <img style="height: 20vh; opacity: 0.2;" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
            {/if}
          </div>

          <div style="margin-top: 8px;">
            <p class="categoria-texto"><b>Nivel de protagonismo</b> <br> Dato categórico | Canal: Símbolo</p>
          </div>

          <div style="margin-top: -8px;" class="btn-group" role="group" aria-label="Basic outlined example">
            <button on:click={() => mostrarSolo(["codPrincipal"], ["codSecundario"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Principal</button>
            <button on:click={() => mostrarSolo(["codSecundario"], ["codPrincipal"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Secundario</button>
          </div>
        </div>

        <div class="cod">
          <div style="justify-content: center; display: flex; align-items: flex-end;">
            {#if codigos.codVivo}
              <img style="height: 21.5vh;" src={imagenGenero["HombrePri"]} alt="HombrePrincipal" />
            {:else}
              <img style="height: 21.5vh; opacity: 0.2;" src={imagenGenero["HombrePri"]} alt="HombrePrincipal" />
            {/if}
            {#if codigos.codMuerto}
              <img style="height: 20vh;" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
            {:else}
              <img style="height: 20vh; opacity: 0.2;" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
            {/if}
          </div>

          <div style="margin-top: 8px;">
            <p class="categoria-texto"><b>Estado de vida</b> <br> Dato categórico | Canal: Símbolo</p>
          </div>

          <div style="margin-top: -8px;" class="btn-group" role="group" aria-label="Basic outlined example">
            <button on:click={() => mostrarSolo(["codVivo"], ["codMuerto"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Vivo</button>
            <button on:click={() => mostrarSolo(["codMuerto"], ["codVivo"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Muerto</button>
          </div>
        </div>
      </div>


      <div class="cod-fila">
        <div class="cod">
          <div>
            {#if codigos.codGryffindor}
              <img style="height: 14vh;" src={imagenCasas["Gryffindor"]} alt="Gryffindor" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenCasas["Gryffindor"]} alt="Gryffindor" />
            {/if}
            {#if codigos.codHufflepuff}
              <img style="height: 14vh;" src={imagenCasas["Hufflepuff"]} alt="Hufflepuff" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenCasas["Hufflepuff"]} alt="Hufflepuff" />
            {/if}
            {#if codigos.codRavenclaw}
              <img style="height: 14vh;" src={imagenCasas["Ravenclaw"]} alt="Ravenclaw" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenCasas["Ravenclaw"]} alt="Ravenclaw" />
            {/if}
            {#if codigos.codSlytherin}
              <img style="height: 14vh;" src={imagenCasas["Slytherin"]} alt="Slytherin" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenCasas["Slytherin"]} alt="Slytherin" />
            {/if}
          </div>

          <div style="margin-top: 8px;">
            <p class="categoria-texto"><b>Casa</b> <br> Dato categórico | Canal: Símbolo </p>
          </div>

          <div style="margin-top: -8px;" class="btn-group" role="group" aria-label="Basic outlined example">
            <button on:click={() => mostrarSolo(["codGryffindor"], ["codHufflepuff", "codRavenclaw", "codSlytherin"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Gryffindor</button>
            <button on:click={() => mostrarSolo(["codHufflepuff"], ["codGryffindor", "codRavenclaw", "codSlytherin"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Hufflepuff</button>
            <button on:click={() => mostrarSolo(["codRavenclaw"], ["codHufflepuff", "codGryffindor", "codSlytherin"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Ravenclaw</button>
            <button on:click={() => mostrarSolo(["codSlytherin"], ["codHufflepuff", "codRavenclaw", "codGryffindor"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Slytherin</button>
          </div>
        </div>

        <div class="cod">
          <div>
            {#if codigos.codBueno}
              <img style="height: 14vh;" src={imagenBando} alt="Bueno" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenBando} alt="Bueno" />
            {/if}
            {#if codigos.codMalo}
              <img style="height: 14vh;" src={imagenBando} alt="Malo" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenBando} alt="Malo" />
            {/if}
          </div>

          <div style="margin-top: 8px;">
            <p class="categoria-texto"><b>Bando</b> <br> Dato categórico | Canal: Color </p>
          </div>

          <div style="margin-top: -8px;" class="btn-group" role="group" aria-label="Basic outlined example">
            <button on:click={() => mostrarSolo(["codBueno"], ["codMalo"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Bueno</button>
            <button on:click={() => mostrarSolo(["codMalo"], ["codBueno"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Malo</button>
          </div>
        </div>

        <div class="cod">
          <div>
            {#if codigos.codAlto}
              <img style="height: 14vh;" src={imagenMagia} alt="Alto" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenMagia} alt="Alto" />
            {/if}
            {#if codigos.codMedio}
              <img style="height: 14vh; opacity: 0.6" src={imagenMagia} alt="Medio" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenMagia} alt="Medio" />
            {/if}
            {#if codigos.codBajo}
              <img style="height: 14vh; opacity: 0.35" src={imagenMagia} alt="Bajo" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenMagia} alt="Bajo" />
            {/if}
          </div>

          <div style="margin-top: 8px;">
            <p class="categoria-texto"><b>Nivel de magia</b> <br> Dato ordinal | Canal: Luminosidad </p>
          </div>

          <div style="margin-top: -8px;" class="btn-group" role="group" aria-label="Basic outlined example">
            <button on:click={() => mostrarSolo(["codAlto"], ["codMedio", "codBajo"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Alto</button>
            <button on:click={() => mostrarSolo(["codMedio"], ["codAlto", "codBajo"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Medio</button>
            <button on:click={() => mostrarSolo(["codBajo"], ["codMedio", "codAlto"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Bajo</button>
          </div>
        </div>
      </div>

      <div class="cod-fila">
        <div class="cod">
          <div>
            {#if codigos.codUno}
              <img style="height: 10vh;" src={imagenLibros[1]} alt="Uno" />
            {:else}
              <img style="height: 10vh; opacity: 0.2;" src={imagenLibros[1]} alt="Uno" />
            {/if}
            {#if codigos.codDos}
              <img style="height: 10vh;" src={imagenLibros[2]} alt="Dos" />
            {:else}
              <img style="height: 10vh; opacity: 0.2;" src={imagenLibros[2]} alt="Dos" />
            {/if}
          </div>

          <div style="margin-top: 8px;">
            <p class="categoria-texto"><b>Apariciones en libro</b> <br> Dato cuantitativo | Canal: Altura </p>
          </div>

          <div style="margin-top: -8px;" class="btn-group" role="group" aria-label="Basic outlined example">
            <button on:click={() => mostrarSolo(["codUno"], ["codDos"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Uno</button>
            <button on:click={() => mostrarSolo(["codDos"], ["codUno"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Dos</button>
            <button style="font-size: 14px;" type="button" class="btn btn-outline-primary">... Siete</button>
          </div>
        </div>

        <div class="cod">
          <div class="cod-mascotas-img">
            {#if codigos.codLechuza}
              <img style="height: 10vh;" src={imagenMascotas["Lechuza"]} alt="Lechuza" />
            {:else}
              <img style="height: 10vh; opacity: 0.2;" src={imagenMascotas["Lechuza"]} alt="Lechuza" />
            {/if}
            {#if codigos.codGato}
              <img style="height: 15vh;" src={imagenMascotas["Gato"]} alt="Gato" />
            {:else}
              <img style="height: 15vh; opacity: 0.2;" src={imagenMascotas["Gato"]} alt="Gato" />
            {/if}
            {#if codigos.codSapo}
              <img style="height: 5vh;" src={imagenMascotas["Sapo"]} alt="Sapo" />
            {:else}
              <img style="height: 5vh; opacity: 0.2;" src={imagenMascotas["Sapo"]} alt="Sapo" />
            {/if}
            {#if codigos.codRata}
              <img style="height: 5vh;" src={imagenMascotas["Rata"]} alt="Rata" />
            {:else}
              <img style="height: 5vh; opacity: 0.2;" src={imagenMascotas["Rata"]} alt="Rata" />
            {/if}
            {#if codigos.codPhoenix}
              <img style="height: 20vh;" src={imagenMascotas["Phoenix"]} alt="Phoenix" />
            {:else}
              <img style="height: 20vh; opacity: 0.2;" src={imagenMascotas["Phoenix"]} alt="Phoenix" />
            {/if}
          </div>

          <div style="margin-top: 8px;">
            <p class="categoria-texto"><b>Mascota (si tiene)</b> <br> Dato categórico | Canal: Símbolo </p>
          </div>

          <div style="margin-top: -8px;" class="btn-group" role="group" aria-label="Basic outlined example">
            <button on:click={() => mostrarSolo(["codLechuza"], ["codGato", "codSapo", "codRata", "codPhoenix"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Lechuza</button>
            <button on:click={() => mostrarSolo(["codGato"], ["codLechuza", "codSapo", "codRata", "codPhoenix"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Gato</button>
            <button on:click={() => mostrarSolo(["codSapo"], ["codGato", "codLechuza", "codRata", "codPhoenix"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Sapo</button>
            <button on:click={() => mostrarSolo(["codRata"], ["codGato", "codSapo", "codLechuza", "codPhoenix"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Rata</button>
            <button on:click={() => mostrarSolo(["codPhoenix"], ["codGato", "codSapo", "codRata", "codLechuza"])}
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">Phoenix</button>
          </div>
        </div>
      </div>

    </div>
    
    

  </main>
  <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.9.2/dist/umd/popper.min.js" integrity="sha384-IQsoLXl5PILFhosVNubq5LC7Qb9DXgDA9i+tQ8Zj3iwWAwPtgFTxbJ8NT4GN1R8p" crossorigin="anonymous"></script>
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.min.js" integrity="sha384-cVKIPhGWiC2Al4u+LWgxfKTRIcfu0JTxR+EQDz/bgldoEyl4H0zUF0QKbrJ0EcQF" crossorigin="anonymous"></script>
</body>



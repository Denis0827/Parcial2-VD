<head>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons/font/bootstrap-icons.css" rel="stylesheet" />
</head>

<script>
  import * as d3 from "d3"
  import personajes from "/src/data/personajes.json"
  import { onMount } from 'svelte';

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

  const imagenBando = {
    Bueno: "/images/bueno.svg",
    Malo: "/images/malo.svg",
  }

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
  
  const luminosidadBrillo = d3.scaleLinear()
    .domain([1, 3])
    .range([0.3, 1]);
  
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

  // Reorganizar todos los personajes en grupos de 8 (4 izquierda, 4 derecha)
  const gruposPersonajes = [];
  for (let i = 0; i < personajes.length; i += 8) {
    gruposPersonajes.push({
      izquierda: personajes.slice(i, i + 4),
      derecha: personajes.slice(i + 4, i + 8)
    });
  }
  
  // Inicializar índice actual
  let grupoActualIndex = 0;

  // Variables para controlar el scroll y las animaciones
  let transitionInProgress = false;
  let scrollDirection = null;
  let lastScrollTime = 0;
  const scrollDebounceTime = 150; // Tiempo mínimo entre transiciones

  // Función para detectar dirección del scroll
  function handleScroll(event) {
    const deltaY = event.deltaY || event.detail || -event.wheelDelta;

    const libroAbajo = document.getElementById('libro-animado');
    const libroArriba = document.getElementById('libro-animado2');

    const rectAbajo = libroAbajo.getBoundingClientRect();
    const rectArriba = libroArriba.getBoundingClientRect();

    const estaEnVistaAbajo = rectAbajo.top <= window.innerHeight && rectAbajo.bottom >= 0;
    const estaEnVistaArriba = rectArriba.top <= window.innerHeight && rectArriba.bottom >= 0;

    // Si no estamos en la zona correspondiente, NO seguimos
    if (deltaY > 0 && !estaEnVistaAbajo) return;
    if (deltaY < 0 && !estaEnVistaArriba) return;

    // Si estamos al principio o final del grupo, permitimos scroll libre
    if (grupoActualIndex === gruposPersonajes.length - 1 && deltaY > 0) return;
    if (grupoActualIndex === 0 && deltaY < 0) return;

    // Bloqueamos scroll manual
    event.preventDefault();

    // Transición en curso o scroll demasiado rápido
    if (transitionInProgress) return;

    const currentTime = Date.now();
    if (currentTime - lastScrollTime < 300) return;
    lastScrollTime = currentTime;

    if (Math.abs(deltaY) < 10) return;

    // Avanzar o retroceder
    if (deltaY > 0 && grupoActualIndex < gruposPersonajes.length - 1) {
      transitionToNextGroup();
    } else if (deltaY < 0 && grupoActualIndex > 0) {
      transitionToPreviousGroup();
    }
  }

  // Función para transición al siguiente grupo
  // Reemplaza las funciones de transición por estas versiones mejoradas:

  function transitionToNextGroup() {
    if (transitionInProgress) return;
    transitionInProgress = true;
    
    // Aplicar animación de desvanecimiento
    const personajeElements = document.querySelectorAll('.personaje-container');
    personajeElements.forEach(el => {
      el.classList.add('fade-out-left');
      // Aseguramos que no hay otras clases de animación
      el.classList.remove('fade-in-right', 'fade-out-up', 'fade-in-down');
    });
    
    setTimeout(() => {
      grupoActualIndex = (grupoActualIndex + 1) % gruposPersonajes.length;
      
      // Pequeño retraso para asegurar que el DOM se actualiza
      setTimeout(() => {
        const newPersonajeElements = document.querySelectorAll('.personaje-container');
        newPersonajeElements.forEach(el => {
          // Limpiar todas las clases de animación y aplicar la nueva
          el.classList.remove('fade-out-left', 'fade-out-up', 'fade-in-down');
          el.classList.add('fade-in-right');
        });
        
        // Tiempo suficiente para completar la animación
        setTimeout(() => {
          newPersonajeElements.forEach(el => {
            // Limpieza final de todas las clases de animación
            el.classList.remove('fade-in-right', 'fade-out-left', 'fade-out-up', 'fade-in-down');
          });
          // Permitir que la siguiente transición ocurra solo después de completar todo
          setTimeout(() => {
            transitionInProgress = false;
          }, 100);
        }, 500);
      }, 100);
    }, 500);
  }

  function transitionToPreviousGroup() {
    if (transitionInProgress) return;
    transitionInProgress = true;
    
    // Aplicar animación de desvanecimiento
    const personajeElements = document.querySelectorAll('.personaje-container');
    personajeElements.forEach(el => {
      el.classList.add('fade-out-up');
      // Aseguramos que no hay otras clases de animación
      el.classList.remove('fade-in-down', 'fade-out-left', 'fade-in-right');
    });
    
    setTimeout(() => {
      grupoActualIndex--;
      
      // Pequeño retraso para asegurar que el DOM se actualiza
      setTimeout(() => {
        const newPersonajeElements = document.querySelectorAll('.personaje-container');
        newPersonajeElements.forEach(el => {
          // Limpiar todas las clases de animación y aplicar la nueva
          el.classList.remove('fade-out-up', 'fade-out-left', 'fade-in-right');
          el.classList.add('fade-in-down');
        });
        
        // Tiempo suficiente para completar la animación
        setTimeout(() => {
          newPersonajeElements.forEach(el => {
            // Limpieza final de todas las clases de animación
            el.classList.remove('fade-in-down', 'fade-out-up', 'fade-out-left', 'fade-in-right');
          });
          // Permitir que la siguiente transición ocurra solo después de completar todo
          setTimeout(() => {
            transitionInProgress = false;
          }, 100);
        }, 500);
      }, 100);
    }, 500);
  }

  // Navegar manualmente entre grupos con los botones
  function navegarGrupo(direccion) {
    if (transitionInProgress) return;
    
    if (direccion === 'anterior' && grupoActualIndex > 0) {
      transitionToPreviousGroup();
    } else if (direccion === 'siguiente' && grupoActualIndex < gruposPersonajes.length - 1) {
      transitionToNextGroup();
    }
  }

  // Configurar event listeners cuando el componente se monta
  onMount(() => {
    // Usar wheel event en lugar de scroll para mejor control
    window.addEventListener('wheel', handleScroll, { passive: false });
    
    // También escuchar eventos de teclado para navegación con flechas
    const handleKeyDown = (event) => {
      if (event.key === 'ArrowDown' || event.key === 'PageDown') {
        event.preventDefault();
        if (grupoActualIndex < gruposPersonajes.length - 1) {
          transitionToNextGroup();
        }
      } else if (event.key === 'ArrowUp' || event.key === 'PageUp') {
        event.preventDefault();
        if (grupoActualIndex > 0) {
          transitionToPreviousGroup();
        }
      }
    };
    
    window.addEventListener('keydown', handleKeyDown);
    
    // Limpiar event listeners cuando el componente se desmonta
    return () => {
      window.removeEventListener('wheel', handleScroll);
      window.removeEventListener('keydown', handleKeyDown);
    };
  });

</script>

<body>
  <main>    

  <div class="home" id="home">
    <nav class="navbar navbar-expand-lg navbar-light fixed-top" style="background-color: var(--color_nav); border-bottom: 1px solid rgba(0, 0, 0, 0.1);">
      <div style="padding-left: 35px; display: flex; gap: 20px;" class="container-fluid">
        <a class="navbar-brand" href="https://www.harrypotter.com/es" target="_blank">
          <img src="/images/logo.png" alt="Logo" width="120px" height="20">
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" 
                aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item">
              <a class="nav-link active" aria-current="page" href="#home">Home</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#representacion">Representación</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#codificacion">Codificación</a>
            </li>
          </ul>
          <!-- Texto a la derecha -->
          <span style="padding-right: 30px;" class="navbar-text ms-auto d-none d-lg-block">
            Marcas y Canales | Visualización de Datos
          </span>
        </div>
      </div>
    </nav>


    <div class="contenedor">
      <img src="/images/fondo.jpg" alt="Fondo" class="fondo" />
      <div class="texto-superpuesto">
        <h1 style="font-family: 'HarryPotter'; font-size: 51px;">Bienvenidos a Hogwarts</h1>
        <h2 style="font-size: 27px;">¿Cuánto conoces de nuestros personajes?</h2>
        <p class="scroll-indicador" style="font-size: 20px; font-weight: bold;">⬇ Scrollea para empezar la visualización</p>
      </div>
    </div>  

  </div>

    <div class="bajada color-fondo">
      <img src="/images/banderas.png" width="300" alt="banderas">
      <div>
        <h2 style="font-family: 'HarryPotter'; font-size: 45px;">Descubre tu casa de <br> Hogwarts</h2>
        <button class="boton">
          <a href="https://www.harrypotter.com/es/sorting-hat" target="_blank"
          style="text-decoration: none; color: white;">Completar Quizz</a>
        </button>
      </div>
    </div>

    <div style="padding: 2rem;" id="representacion" class="color-fondo">
    </div>
                
    <div class="book-container" >
      <div id="libro-animado2"> </div>

      <!-- Imagen del libro como fondo -->
      <img src="/images/librofondo.jpeg" alt="Libro abierto" class="book-background">
        
        <div class="book-pages">
          <div style="margin-top: 80vh;" id="libro-animado"> </div>

          <!-- Página izquierda -->
          <div class="catalogo-container book-page">
            {#each gruposPersonajes[grupoActualIndex].izquierda as personaje}
              <div>
                <div class="personaje-container superpuesto">
                  {#if personaje.genero == "Hombre"}
                    {#if personaje.protagonismo == "Principal"}
                      <img class="personaje {!personaje.vivo ? 'personaje-fantasma' : ''}" src={imagenGenero["HombrePri"]} alt="HombrePrincipal" />
                      <!-- Brillo de varita según nivel de magia -->
                      <img 
                        class="brillo-varita brillo-varita-principal-hombre" 
                        src={imagenMagia} 
                        alt="Brillo" 
                        style="opacity: {luminosidadBrillo(personaje.nivel_magia)}"
                      />
                    {:else}
                      <img class="personaje {!personaje.vivo ? 'personaje-fantasma' : ''}" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
                      <!-- Brillo de varita según nivel de magia -->
                      <img 
                        class="brillo-varita brillo-varita-secundario-hombre" 
                        src={imagenMagia} 
                        alt="Brillo" 
                        style="opacity: {luminosidadBrillo(personaje.nivel_magia)}"
                      />
                    {/if}
                  {:else}
                    {#if personaje.protagonismo == "Principal"}
                      <img class="personaje {!personaje.vivo ? 'personaje-fantasma' : ''}" style="left: 8px;" src={imagenGenero["MujerPri"]} alt="MujerPrincipal" />
                      <!-- Brillo de varita según nivel de magia -->
                      <img 
                        class="brillo-varita brillo-varita-principal-mujer" 
                        src={imagenMagia} 
                        alt="Brillo" 
                        style="opacity: {luminosidadBrillo(personaje.nivel_magia)}"
                      />
                    {:else}
                      <img class="personaje {!personaje.vivo ? 'personaje-fantasma' : ''}" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
                      <!-- Brillo de varita según nivel de magia -->
                      <img 
                        class="brillo-varita brillo-varita-secundario-mujer" 
                        src={imagenMagia} 
                        alt="Brillo" 
                        style="opacity: {luminosidadBrillo(personaje.nivel_magia)}"
                      />
                    {/if}
                  {/if}
                  {#if personaje.genero == "Hombre" && personaje.protagonismo == "Principal"}
                    {#if personaje.bando == "Bueno"}
                      <img class="sombrero" style="left: 2.7vw; top: 3vh;" src={imagenBando["Bueno"]} alt="Bando" />
                    {:else}
                      <img class="sombrero" style="left: 2.7vw; top: 3vh;" src={imagenBando["Malo"]} alt="Bando" />
                    {/if}
                    <img class="libros" style="margin-right: -6vw;" src={imagenLibros[personaje.libros]} alt="Libros" />
                    <img class="casa" style="left: 1.75vw; top: 10.5vh;" src={imagenCasas[personaje.casa]} alt="Casa" />
                  {:else if personaje.genero == "Mujer" && personaje.protagonismo == "Principal"}
                    {#if personaje.bando == "Bueno"}
                      <img class="sombrero" style="left: 2.4vw; top: 4.5vh;" src={imagenBando["Bueno"]} alt="Bando" />
                    {:else}
                      <img class="sombrero" style="left: 2.4vw; top: 4.5vh;" src={imagenBando["Malo"]} alt="Bando" />
                    {/if}
                    <img class="libros" style="margin-right: -5vw;" src={imagenLibros[personaje.libros]} alt="Libros" />
                    <img class="casa" style="left: 1.4vw; top: 11.5vh;" src={imagenCasas[personaje.casa]} alt="Casa" />
                  {:else}
                    {#if personaje.bando == "Bueno"}
                      <img class="sombrero" src={imagenBando["Bueno"]} alt="Bando" />
                    {:else}
                      <img class="sombrero" src={imagenBando["Malo"]} alt="Bando" />
                    {/if}
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
                  {:else if personaje.mascota == "Phoenix"}
                    <img class="phoenix" src={imagenMascotas["Phoenix"]} alt="Phoenix" />
                  {/if}
                </div>

                <div class="nombre-personaje">
                  <p style="margin-top: 8px;">{personaje.personaje}</p>
                </div>

              </div>
            {/each}
          </div>

          <!-- Página derecha -->
          <div class="catalogo-container book-page">
            {#each gruposPersonajes[grupoActualIndex].derecha as personaje}
              <div>
                <div class="personaje-container superpuesto">
                  {#if personaje.genero == "Hombre"}
                    {#if personaje.protagonismo == "Principal"}
                      <img class="personaje {!personaje.vivo ? 'personaje-fantasma' : ''}" src={imagenGenero["HombrePri"]} alt="HombrePrincipal" />
                      <!-- Brillo de varita según nivel de magia -->
                      <img 
                        class="brillo-varita brillo-varita-principal-hombre" 
                        src={imagenMagia} 
                        alt="Brillo" 
                        style="opacity: {luminosidadBrillo(personaje.nivel_magia)}"
                      />
                    {:else}
                      <img class="personaje {!personaje.vivo ? 'personaje-fantasma' : ''}" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
                      <!-- Brillo de varita según nivel de magia -->
                      <img 
                        class="brillo-varita brillo-varita-secundario-hombre" 
                        src={imagenMagia} 
                        alt="Brillo" 
                        style="opacity: {luminosidadBrillo(personaje.nivel_magia)}"
                      />
                    {/if}
                  {:else}
                    {#if personaje.protagonismo == "Principal"}
                      <img class="personaje {!personaje.vivo ? 'personaje-fantasma' : ''}" style="left: 8px;" src={imagenGenero["MujerPri"]} alt="MujerPrincipal" />
                      <!-- Brillo de varita según nivel de magia -->
                      <img 
                        class="brillo-varita brillo-varita-principal-mujer" 
                        src={imagenMagia} 
                        alt="Brillo" 
                        style="opacity: {luminosidadBrillo(personaje.nivel_magia)}"
                      />
                    {:else}
                      <img class="personaje {!personaje.vivo ? 'personaje-fantasma' : ''}" src={imagenGenero["MujerSecu"]} alt="MujerSecundaria" />
                      <!-- Brillo de varita según nivel de magia -->
                      <img 
                        class="brillo-varita brillo-varita-secundario-mujer" 
                        src={imagenMagia} 
                        alt="Brillo" 
                        style="opacity: {luminosidadBrillo(personaje.nivel_magia)}"
                      />
                    {/if}
                  {/if}
                  {#if personaje.genero == "Hombre" && personaje.protagonismo == "Principal"}
                    {#if personaje.bando == "Bueno"}
                      <img class="sombrero" style="left: 2.7vw; top: 3vh;" src={imagenBando["Bueno"]} alt="Bando" />
                    {:else}
                      <img class="sombrero" style="left: 2.7vw; top: 3vh;" src={imagenBando["Malo"]} alt="Bando" />
                    {/if}
                    <img class="libros" style="margin-right: -6vw;" src={imagenLibros[personaje.libros]} alt="Libros" />
                    <img class="casa" style="left: 1.75vw; top: 10.5vh;" src={imagenCasas[personaje.casa]} alt="Casa" />
                  {:else if personaje.genero == "Mujer" && personaje.protagonismo == "Principal"}
                    {#if personaje.bando == "Bueno"}
                      <img class="sombrero" style="left: 2.4vw; top: 4.5vh;" src={imagenBando["Bueno"]} alt="Bando" />
                    {:else}
                      <img class="sombrero" style="left: 2.4vw; top: 4.5vh;" src={imagenBando["Malo"]} alt="Bando" />
                    {/if}
                    <img class="libros" style="margin-right: -5vw;" src={imagenLibros[personaje.libros]} alt="Libros" />
                    <img class="casa" style="left: 1.4vw; top: 11.5vh;" src={imagenCasas[personaje.casa]} alt="Casa" />
                  {:else}
                    {#if personaje.bando == "Bueno"}
                      <img class="sombrero" src={imagenBando["Bueno"]} alt="Bando" />
                    {:else}
                      <img class="sombrero" src={imagenBando["Malo"]} alt="Bando" />
                    {/if}
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
                  {:else if personaje.mascota == "Phoenix"}
                    <img class="phoenix" src={imagenMascotas["Phoenix"]} alt="Phoenix" />
                  {/if}
                </div>

                <div class="nombre-personaje">
                  <p style="margin-top: 8px;">{personaje.personaje}</p>
                </div>

              </div>
            {/each}
          </div>

        </div>
    </div>

    <div class="codificacion-container color-fondo" id="codificacion">
      <h1 class="cod-titulo">Codificación de las marcas</h1>
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
              <img style="height: 20vh;" class="personaje-fantasma" src={imagenGenero["HombreSecu"]} alt="HombreSecundario" />
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
              <img style="height: 14vh;" src={imagenBando["Bueno"]} alt="Bueno" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenBando["Malo"]} alt="Bueno" />
            {/if}
            {#if codigos.codMalo}
              <img style="height: 14vh;" src={imagenBando["Malo"]} alt="Malo" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenBando["Bueno"]} alt="Malo" />
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
              <img style="height: 14vh; opacity: {luminosidadBrillo(1)}" src={imagenMagia} alt="Alto" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenMagia} alt="Alto" />
            {/if}
            {#if codigos.codMedio}
              <img style="height: 14vh; opacity: {luminosidadBrillo(2)}" src={imagenMagia} alt="Medio" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenMagia} alt="Medio" />
            {/if}
            {#if codigos.codBajo}
              <img style="height: 14vh; opacity: {luminosidadBrillo(3)}" src={imagenMagia} alt="Bajo" />
            {:else}
              <img style="height: 14vh; opacity: 0.2;" src={imagenMagia} alt="Bajo" />
            {/if}
          </div>

          <div style="margin-top: 8px;">
            <p class="categoria-texto"><b>Nivel de magia</b> <br> Dato ordinal | Canal: Luminosidad </p>
          </div>

          <div style="margin-top: -8px;" class="btn-group" role="group" aria-label="Basic outlined example">
            <button on:click={() => mostrarSolo(["codAlto"], ["codMedio", "codBajo"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">1</button>
            <button on:click={() => mostrarSolo(["codMedio"], ["codAlto", "codBajo"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">2</button>
            <button on:click={() => mostrarSolo(["codBajo"], ["codMedio", "codAlto"])} 
              style="font-size: 14px;" type="button" class="btn btn-outline-primary">3</button>
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
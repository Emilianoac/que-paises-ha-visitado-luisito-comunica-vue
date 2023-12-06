<template>
  <main class="main-page">
    <!-- NAVBAR -->
    <nav class="main-navbar navbar navbar-expand-lg bg-dark">
      <div class="container-fluid justify-content-start">
        <div class="d-flex justify-content-between align-items-center w-100-lg">
          <a class="navbar-brand" href="/que-paises-ha-visitado-luisito-comunica-vue">
            <img class="img-fluid" src="@/assets/images/luisito.png"/>
          </a>
          <ul class="navbar-nav d-flex flex-row d-lg-none">
            <li class="nav-item">
              <a class="nav-link active" target="_blank" href="https://github.com/Emilianoac/que-paises-ha-visitado-luisito-comunica-vue">
                <i class="fab fa-github"></i>
              </a>
            </li>
          </ul>
        </div>

        <!-- BUSCADOR -->
        <form class="d-flex w-100 mt-1" role="search">
          <div class="input-group">
            <span class="input-group-text"><i class="fas fa-search"></i></span>
            <input 
              type="search" 
              @input="handleSearch($event)" 
              class="form-control" 
              placeholder="Buscar país" 
            />
          </div>

          <!-- RESULTADOS -->
          <div class="resultados-busquedas" v-if="busqueda.resultados.length">
            <ul>
              <li class="resultado" v-for="(pais, i) in busqueda.resultados" :key="i">
                <a 
                  v-if="pais.visitado"
                  class="resultado__contenido" 
                  target="_blank" 
                  :href="pais.video" 
                  title="Ir Al video">
                    <span> 
                      <img class="me-2" :src="pais.bandera.imagen"/>
                      {{ pais.nombre }}
                    </span>
                    <i class="fas fa-check text-success"></i>
                </a>
                <div 
                  v-else
                  class="resultado__contenido" 
                  title="No visitado aún">
                    <span>
                      <img class="me-2" :src="pais.bandera.imagen"/>
                      {{ pais.nombre }}
                    </span> 
                    <i class="fas fa-times-circle text-danger"></i>
                </div>
              </li>
            </ul>
          </div>
        </form>

        <!--LINKS -->
        <ul class="navbar-nav d-none d-lg-flex">
          <li class="nav-item">
            <a class="nav-link active" target="_blank" href="https://github.com/Emilianoac/que-paises-ha-visitado-luisito-comunica-vue"><i class="fab fa-github"></i></a>
          </li>
        </ul>
      </div>
    </nav>
    
    <div class="container-fluid pb-5 pt-4">
      <!--ULTIMA ACTUALIZACION -->
      <div class="row">
        <div class="col-12">
          <p class="text-end small mb-0">Última actualización <strong> 6 de Junio de 2023</strong> </p>
        </div>
      </div>

      <!-- PROGRESO -->
      <div class="row mt-4">
        <div class="col-12">
          <h1 class="text-center"><strong>Luisito Comunica</strong> ha visitado <strong>{{ paisesVisistados.total.length }}</strong> de 195 paises 🌎</h1>
          <span class="d-block"><strong> {{ paisesVisistados.total.length }} / 195</strong> </span>
          <div class="progress" role="progressbar" aria-valuemin="0" aria-valuemax="100">
            <div class="progress-bar" :style="`width: ${ paisesVisistados.porcentaje }%`">
              <div class="d-flex align-items-center justify-content-end px-3">
                <div class="progress-text">
                  <span>🧳✈️</span>
                  <span class="ms-2">{{ paisesVisistados.porcentaje }}%</span>
                </div>
              </div>
            </div>
          </div>
          <span class="small">
            * Para fines de esta pagina se cuentan como visitados aquellos paises que cuenten con 
            1 o más videos en su canal.
          </span>
        </div>
      </div>

      <!-- LISTADO DE PAISES -->
      <div class="listado-continentes mt-4">
        <div class="continente" v-for="(continente, i) in continentes" :key="i">
          <h2 class="continente-nombre">
            <button @click="handleToggle($event, continente.nombre)">
            {{ continente.nombre }} 
            <span><i class="fas fa-chevron-down"></i> </span> 
          </button> 
          </h2>
          <span> {{ paisesVisistados[continente.nombre].length }}</span> / <span>{{ continente.paises.length  }}</span>
          <ul :id="`paises_${continente.nombre}`" class="paises mt-2">
            <li v-for="(pais, i2) in continente.paises" :key="i2">
              <a 
                v-if="pais.visitado" 
                class="pais-content" 
                target="_blank" 
                :href="pais.video" 
                title="Ir Al video">
                  <p class="mb-0">
                    <img :src="pais.bandera.imagen" class="bandera me-2" loading="lazy"/> 
                    {{ pais.nombre }}</p>
                  <i class="fas fa-check text-success"></i>
              </a>
              <div 
                v-else
                class="pais-content" 
                title="No visitado aún">
                  <p class="mb-0">
                    <img :src="pais.bandera.imagen" class="bandera me-2" loading="lazy"/> 
                    {{ pais.nombre }}
                  </p>
                  <i class="fas fa-times text-danger"></i>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup>
  import { reactive,} from 'vue'
  import data from './paises.json'

  const continentes = data.continentes
  const paisesVisistados = reactive({
    total: [] ,
    porcentaje: 0,
    America: [] ,
    Europa: [] ,
    Asia: [] ,
    Oceania: [] ,
    Africa: [] ,
  })
  const busqueda = reactive({
    resultados: [],
  })

  // Calcular la cantidad de paises visitados en cada continente
  continentes.forEach(c => {
    c.paises.sort((a, b) => b.visitado  - a.visitado)
    c.paises.forEach(pais => {
      if(pais.visitado) {
        const nombreContinente = pais.continente 
        paisesVisistados.total.push(pais)
        paisesVisistados[nombreContinente].push(pais)
      }
    })
  })
  // Eliminar repeticion de paises transcontinentales para el total
  paisesVisistados.total = paisesVisistados.total.reduce((accumulator, current) => {
    if (!accumulator.find(item => item.nombre === current.nombre)) {
      accumulator.push(current);
    }
    return accumulator;
  }, [] )

  // Calcular porcentaje de paises visitados
  paisesVisistados.porcentaje = parseFloat(((paisesVisistados.total.length * 100) / 195).toFixed()) 

  // Manejar barra de busqueda
  function handleSearch(e) {
    let search = e.target.value.toLocaleLowerCase()
    search = search.normalize("NFD").replace(/[\u0300-\u036f]/g, "")

    busqueda.resultados = []
    
    if(search !== '' && search.length > 1) {

      continentes.forEach(conti => {
        conti.paises.forEach(pais => {
          if(pais.nombre.toLowerCase().normalize("NFD").replace(/[\u0300-\u036f]/g, "").includes(search)) {

            if(!busqueda.resultados.find(item => item.nombre === pais.nombre)) {
              busqueda.resultados.push(pais)
            }
          }
        })
      })

      if(busqueda.resultados.length === 0) {
        busqueda.resultados.push({
          nombre: `No se encontraron resultados para: ${search}`,
          bandera: {
            emoji: '🤷‍♂️',
            imagen: '',
          }
        })
      } else {
        busqueda.resultados.sort((a, b) => b.visitado - a.visitado )
      }
    } else {

    }
  }

  // Manejar minimización de continentes en movil
  function handleToggle(e, continente) {
    if(matchMedia('(max-width: 1200px)').matches) {
      const listado = document.querySelector(`#paises_${continente}`) 
      listado.classList.toggle('hide')
    }
  }
</script>

<style lang="scss">
  *,
  *::before,
  *::after {
    box-sizing: border-box;
    margin: 0;
    position: relative;
    font-weight: normal;
  }

  body {
    min-height: 100vh;
    color: white;
    background: #181818;
    line-height: 1.6;
    font-family: Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu,
      Cantarell, 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
    font-size: 15px;
    text-rendering: optimizeLegibility;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  .main-page {
    
    .main-navbar {
      position: sticky;
      top: 0;
      z-index: 999;

      .navbar-brand {
        img {
          max-width: 50px;
        }
      }

      form {
        max-width: 500px;
        margin: auto;
      }

      .navbar-nav {

        .nav-link {
          color: white;
        }
      }
    }

    .resultados-busquedas {
      position: absolute;
      width: 100%;
      top: 100%;
      left: 0;
      background: #212529;
      max-height: 300px;
      overflow-y: auto;
      padding: 1em;
      border-radius: 0px 0px 4px 4px;

      ul {
        padding: 0;
        margin: 0;
        list-style-type: none;

        .resultado {
          display: flex;
          justify-content: space-between;
          padding: 0.5em 0.6em;
          border-bottom: 1px solid #343a40;

          .resultado__contenido {
            color: white;
            display: flex;
            align-items: center;
            justify-content: space-between;
            width: 100%;

            span {
              display: flex;

              img {
                object-fit: cover;
                height: 25px;
                display: flex;
                overflow: hidden;
                border-radius: 2px;
              }
            }
          }

          &:hover {
            background: #343a40;
          }
        }
      }
    }

    .progress {
      height: 60px;

      .progress-bar {
        background: linear-gradient(40deg,#fbca57,#ff0e0e);

        .progress-text {
          font-size: 2.4em;
          
          span {
            font-weight: 700;
          }
        }
      }
    }

    .listado-continentes {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      grid-gap: 40px;

      .continente {

        .continente-nombre {
          font-size: 1.8em;
          
          button {
            display: flex;
            align-items: center;
            justify-content: space-between;
            background: none;
            border: none;
            color: white;
            font-weight: 700;
            width: 100%;
            padding: 0;
          }

          span {
            display: none;
          }
        }

        .paises {
          margin: 0;
          padding: 0;
          list-style: none;

          li {
            border-bottom: 1px solid #ffffff35;
            padding: 0.5em;

            .pais-content {
              display: flex;
              align-items: center;
              justify-content: space-between;
              color: white;

              p {
                display: flex;
                align-items: center;

                .bandera {
                  object-fit: scale-down;
                  height: 20px;
                  overflow: hidden;
                  border-radius: 2px;
                }
              }
            }

    
            &:hover {
              background: #ffffff15;
            }

            &:last-of-type {
              border-bottom: none;
            }
          } 
        }
        
        .paises.hide {
          height: 0;
          overflow: hidden;
        }
      }

    }

    @media (max-width: 1200px) {

      .listado-continentes {
        grid-template-columns: 1fr;

        .continente {
          border-bottom: 1px solid #ffffff35;
          padding-bottom: 1em;

          .continente-nombre {

            span {
              display: block;
            }
          }
        }
      }
    }

    @media (max-width: 990px) {
      .w-100-lg {
        width: 100%;
      }
    }

    @media (max-width: 575px) {

      h1 {
        font-size: 1.5em;
      }

      .progress {
        height: 40px;
        
        .progress-bar {

          .progress-text {
            font-size: 1.3em;
          }
        }
      }

      .listado-continentes {

        .continente {

          .continente-nombre {
            font-size: 1.3em;
          }
        }
      }
    }
  }
</style>

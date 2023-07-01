<template>
  <h2 class="subtitulo">Buscá tu canción</h2>
  <div class="containerTitulo"><h1 class="titulo">Musiquera</h1></div>
  <div>
    <div class="containerInput">
      <form @submit.prevent="buscarCancion" class="containerInput">
        <input
          class="input"
          type="text"
          v-model="textoBusqueda"
          placeholder="Ingrese nombre de la canción"
        />
        <button type="submit" class="buttonImg">
          <img src="../assets/outline_search_white_24dp.png" />
        </button>
      </form>
    </div>

    <ul v-if="canciones.length > 0 && !artistaSeleccionado">
      <div class="results">
        <div class="card" v-for="cancion in canciones" :key="cancion.id">
          <p class="tituloCancion">{{ cancion.title }}</p>
          <p>
            <strong>Banda: </strong>
            <span
              class="banda"
              @click="
                mostrarDetallesArtista(cancion['artist-credit'][0].artist)
              "
            >
              {{ cancion["artist-credit"][0].name }}
            </span>
          </p>
          <p v-if="cancion && cancion.length">
            <strong>Duración:</strong> {{ cancion.length / 1000 }} segundos
          </p>
        </div>
      </div>
    </ul>
  </div>

  <div v-if="artistaSeleccionado">
    <div class="resultsArtista">
      <div class="cardArtista">
        <h2>{{ artistaSeleccionado.name }}</h2>
        <h3>Discografia</h3>
        <ul>
          <li v-for="album in discografia" :key="album.id">
            {{ album.title }} ({{ album.date }})
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      textoBusqueda: "",
      canciones: [],
      artistaSeleccionado: null,
      discografia: [],
    };
  },
  methods: {
    buscarCancion() {
      const consulta = encodeURIComponent(`"${this.textoBusqueda}"`);
      const apiUrl = `https://musicbrainz.org/ws/2/recording?fmt=json&query=${consulta}`;

      fetch(apiUrl)
        .then((response) => response.json())
        .then((data) => {
          this.canciones = data.recordings;
          this.artistaSeleccionado = null; // Restablecer los detalles del artista seleccionado
        })
        .catch((error) => {
          console.error("Error al obtener los datos:", error);
        });
    },
    mostrarDetallesArtista(artista) {
      this.artistaSeleccionado = artista;
      const apiUrl = `https://musicbrainz.org/ws/2/release?artist=${artista.id}&fmt=json&type=album`;

      fetch(apiUrl)
        .then((response) => response.json())
        .then((data) => {
          this.discografia = data.releases;
          console.log(this.discografia);
        });
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;700&family=Roboto+Condensed:wght@700&display=swap");

body {
  background-color: #171717;
  font-family: "Montserrat", sans-serif;
}
.titulo {
  margin-top: 100px;
  margin-bottom: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: "Roboto Condensed", sans-serif;
  font-size: 100px;
  color: #ffda2a;
  background-color: rgba(255, 255, 255, 0.108);
  padding: 20px;
  width: 500px;
  border-radius: 20px;
}
.containerTitulo {
  display: flex;
  justify-content: center;
}
.subtitulo {
  color: white;
  display: flex;
  justify-content: center;
  margin-bottom: 50px;

  padding: 15px;
  color: white;

  border-radius: 10px;
}
.artistaDiscografia {
  font-size: 25px;
}
.tituloCancion {
  font-size: 20px;
  font-weight: 700;
}
.results {
  background-color: #ffffff11;
  padding: 20px;
  margin-top: 100px;
  color: white;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
}

.resultsArtista {
  background-color: #ffffff11;
  padding: 20px;
  margin-top: 150px;
  color: white;
  display: flex;
  justify-content: center;
}

.input {
  margin-right: 10px;
  border-radius: 10px;
  width: 500px;
  height: 40px;
  box-shadow: none;
  appearance: none;
  border-style: none;
  padding-left: 20px;
  font-family: "Montserrat", sans-serif;
  font-size: 20px;
}

.card {
  color: black;
  background-color: #ffda2a;
  width: 350px;
  border-radius: 10px;
  padding: 10px;
  margin: 15px;
}

.cardArtista {
  margin: 20px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: black;
  background-color: #ffda2a;
  width: 800px;
  border-radius: 10px;
  font-size: 20px;
}
ul {
  list-style: none;
}
.containerInput {
  display: flex;
  justify-content: center;
  align-items: center;
  padding-bottom: 150px;
}
.buttonImg {
  background-color: #171717;
  border-style: none;
  cursor: pointer;
  padding-top: 5px;
}
.banda {
  color: black;
}
.banda:hover {
  font-weight: 900;
  cursor: pointer;
}
</style>

<template>
  <div>
    <!-- Crear Libro -->
    <div class="row">
      <form @submit.prevent="handleSubmitForm">
        <h5>Crear Libro</h5>
        <div class="row pb-1">
          <div class="col-8">
            <input
              type="text"
              id="inNombre"
              v-model="nuevoLibro.nombre"
              placeholder="Nombre del Libro"
              class="form-control"
              required
            />
          </div>
        </div>

        <div class="row">
          <div class="col-8">
            <input
              type="text"
              id="inAutor"
              v-model="nuevoLibro.autor"
              placeholder="Autor del Libro"
              class="form-control"
              required
            />
          </div>
          <div class="col">
            <button class="btn btn-primary" :disabled="bloquear">Crear</button>
          </div>
        </div>
        <div class="col">
          <div class="row"></div>
        </div>
        <hr />
      </form>
    </div>

    <!-- Alertas -->
    <div class="alert alert-danger" role="alert" v-if="this.mensajeError != ''">
      {{ mensajeError }}
    </div>

    <!-- Lista de Libros -->
    <div class="row">
      <h5>Lista de Libros</h5>
      <div class="row pb-2 d-none">
        <div class="col-6">
          <input
            type="text"
            id="inFiltro"
            placeholder="Todos"
            v-model="filtro"
            class="form-control"
          />
        </div>
        <div class="col-auto px-1">
          <button class="btn btn-primary" :disabled="bloquear">Aplicar</button>
        </div>
        <div class="col-auto px-1">
          <button class="btn btn-secondary" :disabled="bloquear">
            Limpiar
          </button>
        </div>
      </div>

      <!-- Tabla -->
      <div class="row ps-4 pe-1">
        <table class="table table-sm table-bordered">
          <thead>
            <tr id="cent">
              <th scope="col">Id</th>
              <th scope="col">Nombre</th>
              <th scope="col">Autor</th>
              <th scope="col">Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in Libros" :key="item._id">
              <td>{{ item._id.substring(20) }}</td>
              <td>{{ item.nombre_libro }}</td>
              <td>{{ item.autor }}</td>
              <td id="cent">
                <!-- <button
                  @click.prevent="editarLibro(item._id, item.nombre_libro)"
                  class="btn btn-outline-success btn-sm p-1"
                  :disabled="item.id_prestamo!=null"
                >
                  Editar
                </button> -->
                <button
                  @click.prevent="borrarLibro(item._id, item.nombre_libro)"
                  class="btn btn-outline-danger btn-sm p-1"
                  :disabled="item.id_prestamo!=null"
                >
                  Borrar
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      nuevoLibro: {
        nombre: "",
        autor: "",
      },
      filtro: "",
      mensajeError: "",
      bloquear: true,
      Libros: [],
      libroBorrar: {
        id: "",
        nombre: "",
      },
    };
  },

  created() {
    console.log("Listando Libros");
    this.listarLibros();
  },

  methods: {
    handleSubmitForm() {
      // Crear Libro
      this.bloquear = true;

      let apiURL =
        "http://localhost:3000/api/nuevo-libro"; //Enrutamiento con el back
      console.log(apiURL);

      axios.post(apiURL, {
          nombre_libro: this.nuevoLibro.nombre,
          autor: this.nuevoLibro.autor,
          id_prestamo: null
        })
        .then((res) => {
          console.log(res.data);
          this.listarLibros();
        })
        .catch((error) => {
          console.log(error);
          this.mensajeError =
            "Error creando nuevo Libro, verifique el Nombre y Autor e intentelo numevamente.";
          this.bloquear = false;
        });
    },

    listarLibros() {
      axios
        .get("http://localhost:3000/api/libro")
        .then((res) => {
          this.Libros = res.data.reverse();
          // console.log(JSON.stringify(this.Libros));
          this.mensajeError = "";
          this.bloquear = false;
        })
        .catch((error) => {
          console.log(error);
          this.mensajeError = "No se puede traer la lista de Libros.";
        });

      this.nuevoLibro.nombre = "";
      this.nuevoLibro.autor = "";
      this.filtro = "";
    },

    borrarLibro(id, nombre) {
      console.log("Borrando " + nombre);
      axios
        .delete("http://localhost:3000/api/libro/" + id)
        .then((res) => {
          console.log(res.data);
          this.listarLibros();
        })
        .catch((error) => {
          console.log(error);
          this.mensajeError = "No se borro Libro " + nombre;
        });
    },

    editarLibro(id, nombre) {
      alert("No Implementado! (" + id + " - " + nombre + ")");
    },
  },
};
</script>

<style scoped>
a {
  text-decoration: none;
}

#cent {
  text-align: center;
}
</style>
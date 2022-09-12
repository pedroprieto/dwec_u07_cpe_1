<template>
  <div>
    <div class="form-group row">
      <label class="sr-only col col-form-label" for="githubUser">Usuario de GitHub:</label>
      <input placeholder="Introducir nombre de usuario de GitHub" id="githubUser" class="col form-control" :disabled="disabled" v-on:keydown.enter="obtenerUsuario" v-model="user" type="text">
    </div>
    <div class="row alert alert-danger" role="alert" v-if="isError">
      El usuario no existe
    </div>
    <div class="row justify-content-center">
      <transition name="aparecer">
        <div v-if="validUser" class="card col-sm-4" >
          <img :src="userData.avatar_url" class="img-fluid card-img-top" alt="avatar">
          <div class="card-body">
            <h5 class="card-title">{{userData.name}}</h5>
            <button v-on:click="obtenerRepos" class="btn btn-primary">Repositorios</button>
            <a class="card-link" :href="userData.html_url">URL de GitHub</a>
          </div>
        </div>
      </transition>
      <transition name="aparecer">
        <div class="col-sm-8" v-if="showRepos">
          <div class="list-group">
            <GitHubRepo v-for="repo in repos" :key="repo.id" :repo="repo"></GitHubRepo>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
  import GitHubRepo from './GitHubRepo.vue'

  export default {
      name: 'GitHub',
      components: {
          GitHubRepo
      },
      data: function() {
          return {
              user: '',
              userData: {},
              repos: [],
              isError : false,
              disabled: false,
              showRepos: false,
              validUser: false
          }
      },
      methods: {
          obtenerUsuario: function() {
              // Función para obtener los datos de usuario de la API de GitHub

              // Deshabilitar campo de texto
              this.disabled = true;

              // Resetear variable de error
              this.isError = false;

              // No mostrar lista de repositorios
              this.showRepos = false;

              // Crear URL del usuario
              var url = `https://api.github.com/users/${this.user}`;

              // Petición AJAX
              fetch(url)
                  .then(response => {
                      if (response.ok) {
                          return response.json();
                      } else {
                          throw new Error("Usuario no encontrado");
                      }
                  })
                  .then(datos => {
                      this.userData = datos;
                      // Indicamos que el usuario es válido
                      this.validUser = true;
                  })
                  .catch(error => {
                      // La petición se ha encontado con un error (posiblemente, usuario inexistente)
                      error;
                      // Indicamos que no se han cargado correctamente los datos de usuario
                      this.validUser = false;

                      // Indicar que hay un error
                      this.isError = true;
                  })
                  .finally(() => {
                      // Al terminar, volver a habilitar el campo de texto
                      this.disabled = false;
                  })

          },
          obtenerRepos: function() {
              // Función para obtener los repositorios del usuario desde la API de GítHub
              // La URL de los repositorios de usuario se puede obtener a través del campo 'repos_url' de los datos del usuario
              var url = this.userData.repos_url;

              // Petición AJAX
              fetch(url)
                  .then(response => {
                      if (response.ok) {
                          return response.json();
                      } else {
                          throw new Error("Usuario no encontrado");
                      }
                  })
                  .then(datos => {

                      // La petición es válida. Almacenamos los datos de la respuesta en la variable local 'repos'
                      this.repos = datos;
                      // Mostrar lista de repositorios
                      this.showRepos = true;
                  })


                  .catch(error => {
                      // La petición no es válida. Indicar que no se han cargado correctamente los repositorios
                      error;
                      this.validUser = false;
                      this.showRepos = false;

                      // Indicar que hay un error
                      this.isError = true;
                  })
          }
      }
  }
</script>

<style scoped>
  .aparecer-enter-active,
  .aparecer-leave-active {
      position: relative;
      transition: opacity 1s;
  }

  .aparecer-enter-from,
  .aparecer-leave-to {
      opacity: 0;
  }
</style>

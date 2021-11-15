<template>
  <div id="app">
    <form id="inputform" method="POST" @submit.prevent="postAxios">
      <div class="input-group mb-3">
        <div class="input-group-append">
          <span class="input-group-text" id="basic-addon2">Nombre</span>
        </div>
        <input type="text" id="nombre" name="nombre" v-model="nombre" class="form-control" placeholder="Inserte su nombre" aria-label="Inserte su nombre" required/>
      </div>
      <div class="input-group mb-3">
        <div class="input-group-append">
          <span class="input-group-text" id="basic-addon2">Edad</span>
        </div>
        <input type="number" id="edad" name="edad" v-model="edad" class="form-control" placeholder="Inserte su edad" aria-label="Inserte su edad" required/>
      </div>
      <div class="input-group mb-3">
        <div class="input-group-append">
          <span class="input-group-text" id="basic-addon2">Email</span>
        </div>
        <input type="email" id="email" name="email" v-model="email" class="form-control" placeholder="Inserte su mail" aria-label="Inserte su mail" required/>
      </div>

      <div class="alert alert-warning" v-if="errores.length">
          <p v-for="error in errores" :key="error">{{ error }}</p>
      </div>

      <input class="btn btn-warning mx-1" type="submit" value="Post"/>
    </form>

    <br/>

    <div class="container">
      <button class="btn btn-primary mx-1" @click="getXMLHttpRequest()">Get XHR</button> 
      <button class="btn btn-primary mx-1" @click="getFetch()">Get Fetch</button> 
      <button class="btn btn-primary mx-1" @click="getAxios()">Get Axios</button>
    </div>

    <br/>

    <div class="table-responsive-sm">
      <table class="table">
        <thead class="thead-light">
          <tr>
            <th>Nombre</th>
            <th>Edad</th>
            <th>Email</th>
          </tr>
        </thead>
        <tr v-for="persona in respuesta" :key="persona.id">
          <td>{{persona.nombre}}</td>
          <td>{{persona.edad}}</td>
          <td>{{persona.email}}</td>
        </tr>
      </table>
    </div>

  </div>

</template>

<script>
const axios = require('axios').default
const serializer = require('form-serialize');

const JSON_URL = 'https://61861c64cd8530001765a9b6.mockapi.io/usuarios'

export default {
  name: 'App',
  data(){
    return {
      respuesta: "",
      nombre: null,
      edad: null,
      email: null,
      errores: []
    }
  },
  methods: {
    /* GET methods */
    getXMLHttpRequest(){
      let xhr = new XMLHttpRequest();

      xhr.open("GET", JSON_URL);
      xhr.addEventListener('load', () => {
        if(xhr.status == 200){
          this.respuesta = JSON.parse(xhr.response)
        }
      })
      xhr.send(null);
    },
    getFetch(){
      fetch(JSON_URL)
      .then(response => response.json())
      .then(JSONresponse => {
        this.respuesta = JSONresponse
      })
      .catch(error => console.log(error))
    },
    getAxios(){
      axios.get(JSON_URL)
      .then(response => {
        this.respuesta = response['data']
      })
      .catch(error => console.log(error))
    },
    /* POST */
    postAxios(event){
      event.preventDefault()

      this.validateForm();

      if(this.errores.length == 0){
        let form = document.querySelector('#inputform');
        let obj = serializer(form, {hash: true});

        axios.post(JSON_URL, obj)
        .then(response => {
          console.log(response);
          console.log(response.data);
        })
        .then(response => {
          this.getAxios()
          console.log(response)
        })
        .catch(error => error)
      }
    },
    validateForm(){
      this.errores = []

      if(this.nombre.length < 5 || this.nombre.length > 15){
        this.errores.push('El nombre debe tener entre 5 y 15 caracteres');
      }
      if(this.edad < 18 || this.edad > 120){
        this.errores.push('La edad permitida es entre 18 y 120 años');
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

@import '~bootstrap/dist/css/bootstrap.min.css';
</style>

<template>
  <div>
    <h1>Lista Aplicantes</h1>
    <v-data-table
      :headers="headers"
      :items="envios"
      :items-per-page="5"
      class="elevation-1"
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="aceptar(item)" color="#8bc34a">
          mdi-check
        </v-icon>
        <v-icon small class="mr-2"  color="#e64a19">
          mdi-delete
        </v-icon>
      </template>
    </v-data-table>
  </div>
</template>
<script>
export default {
  name: "listPage",
  beforeMount() {
    this.loadPage();
  },
  beforeUpdate() {
    try {
      this.$refs.formConvo.validate();
    } catch (error) {}
  },
  data() {
    return {
      formConvo: null,
      headers: [
          { text: "Id Convocatoria", value: "id" },
        { text: "Id Usuario", value: "id" },
        { text: "Nombre", value: "first_name" },
        { text: "Apellido", value: "last_name" },
        { text: "Email", value: "email" },
        { text: "Acciones", value: "actions" }
      ],
      envios:[],
      envio: {
        id: null,
        first_name: null,
        last_name: null,
        email: null,
      },
      dialog: false,
    };
  },
  methods: {
    loadPage() {
      this.loadEnvio();
    },
    loadEnvio(envio){
        let url = "http://localhost:3001/envios";
        this.$axios.get(url).then(response => {
        let data = response.data;
        this.envios = data;
      });
    },
    loadConvo(convo){
        let url = "http://localhost:3001/convos";
        this.$axios.get(url).then(response => {
        let data = response.data;
        this.convo = data;
      });
    },
    loadCarga(envio){
        this.envio = Object.assign({}, envio);
    },
    aceptar(acep){
          let url = "http://localhost:3001/lista";
          this.$axios.post(url, this.envio).then(response => {
            this.loadCarga();
            this.envio = {};
            this.$swal.fire(
              "Enviado",
              "Aceptaste al candidato.!",
              "success"
            );
          });
        
        }
    },
    deleteEnvio(convo) {
    
    },
  
};
</script>
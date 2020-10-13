<template>
  <div>
    <h1>Aplicar convocatorias</h1>
    <v-data-table
      :headers="headers"
      :items="convos"
      :items-per-page="5"
      class="elevation-1"
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="aplicar(item)" color="#8bc34a">
          mdi-check
        </v-icon>
      </template>
    </v-data-table>
  </div>
</template>
<script>
export default {
  name: "ConvoPage",
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
        { text: "Tipo de Trabajo", value: "tipoTrabajo" },
        { text: "Local", value: "localidades" },
        { text: "Actions", value: "actions" }
      ],
      envios:[],
      envio: {
        identification_type: null,
        id: null,
        //id_convo: convo.id,
        first_name: null,
        last_name: null,
        email: null,
      },
      convos: [],
      convo: {
          localidades: null,
        TDT: null,
        id: null,
        tipoTrabajo: null,
      },
        localidades: [],
      TDT: [],
      dialog: false,
    };
  },
  methods: {
    loadPage() {
      this.loadTDT();
      this.loadConvos();
      this.loadLocalidades();
    },

    loadTDT() {
      let url = "http://localhost:3001/TDT";
      this.$axios.get(url).then(response => {
        let data = response.data;
        this.TDT = data;
      });
    },
    loadLocalidades() {
      let url = "http://localhost:3001/localidades";
      this.$axios.get(url).then(response => {
        let data = response.data;
        this.localidades = data;
      });
    },
    loadConvos() {
      let url = "http://localhost:3001/convos";
      this.$axios.get(url).then(response => {
        let data = response.data;
        this.convos = data;
      });
    },
    loadConvo(convo) {
      this.convo = Object.assign({}, convo);
      this.editing = true;
    },
    loadEnvio(envio){
      this.envio = Object.assign({}, envio);
    },
    aplicar(envio){
        //Save envios
        // Validación si la identificación de una persona ya existe en el array
          let url = "http://localhost:3001/envios";
          this.$axios.post(url, this.envio).then(response => {
            this.loadEnvio();
            this.envio = {};
            this.$swal.fire(
              "Enviado",
              "Aplicaste para una convocatoria.!",
              "success"
            );
          });
        
        }
    },
    deleteEnvio(convo) {
    
    },
  
};
</script>
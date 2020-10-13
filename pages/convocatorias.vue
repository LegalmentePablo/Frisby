<template>
  <div>
    <h1>Generar Convocatorias</h1>
    <v-form v-model="formConvo" ref="formConvo">
      <v-container>
        <v-row>
          <v-col cols="12" md="6">
            <v-select
              :items="localidades"
              item-value="id"
              item-text="name"
              :rules="fieldRequired"
              required
              v-model="convo.localidades"
              label="Localidad"
            ></v-select>
          </v-col>

          <v-col cols="12" md="6">
            <v-select
              :items="TDT"
              item-value="id"
              item-text="name"
              :rules="fieldRequired"
              required
              v-model="convo.TDT"
              label="Locales"
            ></v-select>
          </v-col>

          <v-col cols="12" md="6">
            <v-text-field
              v-model="convo.id"
              :rules="fieldRequired"
              :disabled="editing"
              label="ID Convocatoria"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              v-model="convo.tipoTrabajo"
              :rules="fieldRequired"
              label="Tipo de trabajo"
              required
            ></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-btn color="primary" @click="save()" v-if="!editing">Crear</v-btn>
            <v-btn color="#8bc34a" @click="editConvo()" v-else>Editar</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>

    <v-data-table
      :headers="headers"
      :items="convos"
      :items-per-page="5"
      class="elevation-1"
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="loadConvo(item)">
          mdi-pencil
        </v-icon>
        <v-icon small @click="deleteConvo(item)">
          mdi-delete
        </v-icon>
      </template>
    </v-data-table>

    <!-- Dialogo de alerta cuando el formulario no es valido -->
    <v-dialog v-model="dialog" max-width="290">
      <v-card>
        <v-card-title class="headline">
          Error
        </v-card-title>

        <v-card-text>
          Form required some fields
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>

          <v-btn color="accent" text @click="dialog = false">
            Close
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
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
      convos: [],
      convo: {
        localidades: null,
        TDT: null,
        id: null,
        tipoTrabajo: null
      },
      localidades: [],
      TDT: [],
      fieldRequired: [v => !!v || "Field is required"],
      dialog: false,
      // Si esta editando o no
      editing: false
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
    save() {
      if (this.$refs.formConvo.validate() && this.formConvo) {
        //Save convos
        let exist = this.convos.find(x => x.id == this.convo.id);
        // Validación si la identificación de una persona ya existe en el array
        if (exist == undefined) {
          let url = "http://localhost:3001/convos";
          this.$axios.post(url, this.convo).then(response => {
            this.loadConvo();
            this.convo = {};
            this.$swal.fire(
              "Creado",
              "La persona ha sido creada correctamente.!",
              "success"
            );
          });
        } else {
          $swal.fire({
            icon: "error",
            title: "Oops...",
            text: "La persona ya existe en la tabla."
          });
        }
      } else {
        this.dialog = true;
      }
    },
    loadConvo(convo) {
      this.convo = Object.assign({}, convo);
      this.editing = true;
    },
    editConvo() {
      console.log(" -- modificar la persona con los datos cargados -- ");
      if (this.$refs.formConvo.validate() && this.formConvo) {
        // Encontrar la posición que esta el usuario en el array
        let existIndex = this.convos.findIndex(x => x.id == this.convo.id);
        // Validación si la identificación de una persona ya existe en el array
        if (existIndex > -1) {
          console.log(
            "La persona existe y esta en la posición del array",
            existIndex
          );
          //Modificar la persona del array
          let url = "http://localhost:3001/convos/" + this.convo.id;
          this.$axios.put(url, this.convo).then(response => {
            this.convo = {};
            this.editing = false;
            this.$swal.fire(
              "Modificado.",
              "La persona ha sido modificada correctamente.!",
              "success"
            );
            this.loadConvos();
          });
        } else {
          this.$swal.fire({
            icon: "error",
            title: "Oops...",
            text: "La persona NO existe en la tabla."
          });
        }
      } else {
        this.dialog = true;
      }
    },
    deleteConvo(convo) {
      console.log(convo);
      let existIndex = this.convos.findIndex(x => x.id == convo.id);
      if (existIndex > -1) {
        this.$swal
          .fire({
            title: "Desea eliminar el usuario?",
            text: "Este cambio no se puede revertir.",
            type: "warning",
            showCancelButton: true,
            confirmButtonColor: "#3085d6",
            cancelButtonColor: "#d33",
            confirmButtonText: "Si, eliminalo!",
            cancelButtonText: "Cancelar"
          })
          .then(result => {
            console.log(result);
            if (result.value) {
              let url = "http://localhost:3001/convos/" + convo.id;
              this.$axios.delete(url).then(response => {
                this.$swal.fire(
                  "Eliminado.",
                  "La persona ha sido eliminada correctamente.!",
                  "success"
                );
                this.loadConvos();
              });
            }
          });
      } else {
        this.$swal.fire({
          icon: "error",
          title: "Oops...",
          text: "La persona NO existe en la tabla."
        });
      }
    }
  }
};
</script>

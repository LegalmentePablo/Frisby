<template>
  <div class="spacing-playground pa-10">
    <h1>Registro</h1>
    <v-form v-model="formUsers" ref="formUsers">
      <v-container>
        <v-row>
          <v-col cols="12" md="6">
            <v-select
              :items="identification_types"
              item-value="id"
              item-text="name"
              :rules="fieldRequired"
              required
              v-model="user.identification_type"
              label="Tipo de Documento"
            ></v-select>
          </v-col>

          <v-col cols="12" md="6">
            <v-text-field
              v-model="user.id"
              :rules="fieldRequired"
              :disabled="editing"
              label="Documento"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              v-model="user.firstname"
              :rules="fieldRequired"
              label="Nombre"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              v-model="user.lastname"
              :rules="fieldRequired"
              label="Apellidos"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              v-model="user.email"
              :rules="emailRules"
              label="Email"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              v-model="user.password"
              type="password"
              :rules="fieldRequired"
              label="Contraseña"
              required
            ></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              v-model="confirmarContra"
              type="password"
              :rules="fieldRequired"
              label="Confirmar contraseña"
              required
            ></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-checkbox
              required
              :rules="fieldRequired"
              v-model="user.accepted"
              :label="`Accept policies?`"
            ></v-checkbox>
          </v-col>
          <v-col cols="12" md="4">
            <v-btn color="primary" @click="save()">Registrarse</v-btn>
            <v-btn color="#e3e3e3" @click="volver()">Volver</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>

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

          <v-btn color="#8bc34a" text @click="dialog = false">
            Cerrar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>
<script>
export default {
  layout: "blank",
  name: "registroPage",
  confirmarContra: null,
  beforeMount() {
    this.loadPage();
  },
  beforeUpdate() {
    try {
      this.$refs.formUsers.validate();
    } catch (error) {}
  },
  data() {
    return {
      formUsers: null,
      users: [],
      user: {
        identification_type: null,
        id: null,
        firstname: null,
        lastname: null,
        email: null,
        password: null,
        accepted: null,
        rol: null
      },
      identification_types: [],
      fieldRequired: [v => !!v || "Field is required"],
      emailRules: [
        v => !!v || "E-mail is required",
        v => /.+@.+/.test(v) || "E-mail must be valid"
      ],
      dialog: false
    };
  },
  methods: {
      volver(){
       this.$router.push("/");
    },
    loadPage() {
      this.loadIdenticationTypes();
    },
    loadIdenticationTypes() {
      let url = "http://localhost:3001/identification_type";
      this.$axios.get(url).then(response => {
        let data = response.data;
        this.identification_types = data;
      });
    },
    save() {
      if (this.$refs.formUsers.validate() && this.formUsers) {
        //Save users
        let exist = this.users.find(x => x.id == this.user.id);
        // Validación si la identificación de una persona ya existe en el array
        if (exist == undefined) {
          let url = "http://localhost:3001/users";
          this.$axios.post(url, this.user).then(response => {
            this.loadUsers();
            this.user = {};
            this.$swal.fire(
                'Registro!',
                'La persona ha sido creada correctamente.!',
                'success'
            );
          });
        } else {
          this.$swal.fire({
            icon: 'error',
            title: 'Oops...',
            text: 'La persona ya existe en la tabla.'
          });
        }
      } else {
        this.dialog = true;
      }
    }
  }
};
</script>

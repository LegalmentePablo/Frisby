<template>
  <div>
    <h1>Usuario</h1>
    <v-form v-model="formUsers" ref="formUsers">
      <v-container>
        <v-row>
          <v-col cols="12" md="6">
            <v-select
              :items="identification_type"
              item-text="name"
              item-value="id"
              :rules="fieldRequired"
              required
              v-model="user.identification_type"
              label="Documento"
            ></v-select>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              v-model="user.id"
              :rules="fieldRequired"
              :disabled="editing"
              label="Identificación"
              required
            ></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              v-model="user.first_name"
              :rules="fieldRequired"
              label="Nombre"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              v-model="user.last_name"
              :rules="fieldRequired"
              label="Apellido"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              v-model="user.email"
              :rules="emailRules"
              label="E-mail"
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
          <v-col cols="12" sm="6" md="4">
            <v-checkbox
              v-model="user.terminos"
              label="Politicas de privacidad"
              hide-details
              :rules="fieldRequired"
              required
            ></v-checkbox>
          </v-col>
          <v-col cols="12" md="4">
            <v-btn color="primary" @click="save()" v-if="!editing"
              >Guardar</v-btn
            >
            <v-btn color="primary" @click="editUser()" v-else>Editar</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
    <v-data-table
      :headers="headers"
      :items="users"
      :items-per-page="5"
      class="elevation-1"
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="loadUser(item)">
          mdi-pencil
        </v-icon>
        <v-icon small @click="deleteUser(item)">
          mdi-delete
        </v-icon>
      </template>
    </v-data-table>
    <v-dialog v-model="dialog" max-width="290">
      <v-card>
        <v-card-title class="headline">Error</v-card-title>

        <v-card-text>
          Llene los formularios restantes
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="#8bc34a" text @click="dialog = false">
            Aceptar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  name: "paginaUsuarios",
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
      headers: [
        { text: "Documento", value: "id" },
        { text: "Nombre", value: "first_name" },
        { text: "Email", value: "email" },
        { text: "Acciones", value: "actions" }
      ],
      users: [],
      user: {
        identification_type: null,
        id: null,
        first_name: null,
        last_name: null,
        email: null,
        password: null,
        terminos: null,
        rol: null
      },
      identification_type: [],
      fieldRequired: [v => !!v || "Field is required"],
      emailRules: [
        v => !!v || "E-mail is required",
        v => /.+@.+/.test(v) || "E-mail must be valid"
      ],
      dialog: false,
      editing: false
    };
  },
  methods: {
    loadPage() {
      let users = localStorage.getItem("users");
      this.users = JSON.parse(users);
      this.loadIdentificationTypes()
    },
    loadIdentificationTypes(){
      let url = "http://localhost:3001/identification_type";
      this.$axios.get(url).then(response =>{
        let data = response.data;
        this.identification_type = data;
        console.log(response);
      })
    },
    save() {
      if (this.$refs.formUsers.validate() && this.formUsers) {
        let existe = this.users.find(
          x => x.id == this.user.id
        );
        if (existe == undefined) {
          this.users.push(this.user);
          localStorage.setItem("users", JSON.stringify(this.users));
          //this.listPersons();
          this.user = {};
          this.$swal.fire("Guardado!", "Se creo el usuario!", "success");
        } else {
          this.$swal.fire({
            icon: "error",
            title: "Oops...",
            text: "Algo salio mal!",
          });
        }
      } else {
        this.dialog = true;
      }
    },
    editUser() {
      if (this.$refs.formUsers.validate() && this.formUsers) {
        let existe = this.users.findIndex(
          x => x.id == this.user.id
        );
        if (existe > -1) {
          this.users.splice(existe, 1, this.user);
          this.user = {};
          this.editing = false;
          localStorage.setItem("users", JSON.stringify(this.users));
          alert("La persona ha sido modificada");
        } else {
          alert("Error");
        }
      } else {
        this.dialog = true;
      }
    },
    loadUser(user) {
      this.user = user;
      this.editing = true;
    },
    deleteUser(user) {
      let existe = this.users.findIndex(
        x => x.id == user.id
      );
      if (existe > -1) {
        let confirmar = confirm("Esta seguro de eliminarlo?");
        if (confirmar) {
          this.users.splice(existe, 1);
          localStorage.setItem("users", JSON.stringify(this.users));
          alert("Se elimino");
        }
      }
    }
  }
};
</script>

<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" absolute temporary>
      <v-list-item>

        <v-list-item-content>
          <v-list-item-title>Menu</v-list-item-title>
        </v-list-item-content>
      </v-list-item>

      <v-divider></v-divider>

      <v-list dense>
        <v-list-item :to="item.route" v-for="item in items_menu" :key="item.id">
          <v-list-item-icon>
            <v-icon color="#f57c00">{{item.icon}}</v-icon>
          </v-list-item-icon>
          <v-list-item-content>
            <v-list-item-title style="padding:6px;">{{item.title}}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar app color="#FBC02D" dense>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Frisby</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn color="#d32f2f" @click="logout()">
        Salir
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-container> <nuxt /> </v-container>
    </v-main>
  </v-app>
</template>
<script>
export default {
   data () {
      return {
        drawer: null,
        items_menu: [
          {id:"01", title: 'Home', icon: 'mdi-home', route: "/home" },
          {id:"02", title: 'Usuarios', icon: 'mdi-account-group', route: "/users" },
          {id:"03", title: 'Convocatorias', icon: 'mdi-book-account-outline', route: "/convocatorias" },
          {id:"04", title: 'Aplicar', icon: 'mdi-clipboard-check-outline', route: "/aplicar" },
          {id:"05", title: 'Lista de Aplicantes', icon: 'mdi-clipboard-list-outline', route: "/listaConvo" },
        ],
      }
    },
  methods: {
    logout() {
      this.$router.push("/");
    }
  }
};
</script>
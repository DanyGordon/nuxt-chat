<template>
  <v-app app dark>
    <v-navigation-drawer app v-model="drawer" mobile-break-point="650">
      <v-list subheader>
        <v-subheader>Учасники комнаты {{ user.room }}</v-subheader>

        <v-list-item
          v-for="u in users"
          :key="u.id"
        >
          <v-list-item-content>
            <v-list-item-title>{{ u.name }}</v-list-item-title>
          </v-list-item-content>

          <v-list-item-icon>
            <v-icon :color="u.id === user.id ? 'primary' : 'grey'">
              mdi-message-outline
            </v-icon>
          </v-list-item-icon>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar app>
      <v-app-bar-nav-icon @click="toogleDrawer"></v-app-bar-nav-icon>
      <v-btn icon @click="exit">
        <v-icon>mdi-arrow-left</v-icon>
      </v-btn>
      <v-toolbar-title>Чат-комната {{ user.room }}</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <div style="height: 100%">
        <nuxt />
      </div>
    </v-main>
  </v-app>
</template>

<script>
import { mapState, mapMutations } from 'vuex'

export default {
  data: () => ({
    drawer: true,
  }),
  computed: mapState(['user', 'users']),
  methods: {
    ...mapMutations(['clearData']),
    exit() {
      this.$socket.emit('userLeft', this.user.id, () => {
        this.$router.push('/?message=leftChat');
        this.clearData();
      })
    },
    toogleDrawer() {
      this.drawer = !this.drawer
    }
  }
}
</script>

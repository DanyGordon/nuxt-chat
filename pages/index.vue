<template>
  <v-layout
    column
    justify-center
    align-center
  >
    <v-flex xs12 sm8>
      <v-snackbar
        :timeout="6000"
        v-model="snackbar"
        top
      >
        {{ message }}
        <v-btn class="ml-3" color="pink" plain @click="snackbar = false">Закрыть</v-btn>
      </v-snackbar>
      <v-card min-width="400" class="mx-auto my-12">
        <v-card-title class="mb-5">
          <h1>Nuxt чат</h1>
        </v-card-title>
        <v-card-text>
          <v-form
            ref="form"
            v-model="valid"
            lazy-validation
          >
            <v-text-field
              v-model="name"
              :counter="16"
              :rules="nameRules"
              label="Ваше имя"
              required
            ></v-text-field>

            <v-text-field
              v-model="room"
              :rules="roomRules"
              label="Введите комнату"
              required
            ></v-text-field>

            <v-btn
              :disabled="!valid"
              color="primary"
              class="mr-4"
              @click="submit"
            >
              Войти
            </v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
  import { mapMutations } from 'vuex'
  import messages from '@/plugins/messages'

  export default {
    sockets: {
      connect() {
        console.log('Client IO connected!');
      }
    },
    layout: 'empty',
    head: {
      title: 'Добро пожаловать в Nuxt чат'
    },
    data: () => ({
      valid: true,
      name: '',
      nameRules: [
        v => !!v || 'Введите имя',
        v => (v && v.length <= 16) || 'Имя не должно превышать 16 символов',
      ],
      room: '',
      roomRules: [
        v => !!v || 'Введите комнату',
      ],
      snackbar: false,
      message: ''
    }),
    mounted() {
      if(this.$route.query.message) {
        this.snackbar = true
        this.message = messages[this.$route.query.message]; 
      }
    },
    methods: {
      ...mapMutations(['setUser']),
      submit () {
        if (this.$refs.form.validate()) {
          const user = {
            name: this.name,
            room: this.room
          }
          this.$socket.emit('userJoined', user, data => {
            console.log('Data recieved', data);
            if(typeof data === 'string') {
              console.error(data);
            } else {
              user.id = data.userId;
              this.setUser(user);
              this.$router.push('/chat');
            }
          })
        }
      },
    },
  }
</script>

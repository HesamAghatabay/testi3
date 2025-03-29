<template>
  <q-page padding>
    <!-- content -->
    <h3>Login Page</h3>
    <div class="row">
      <div class="col-6">
        <q-input class="q-mx-lg" v-model="mobile" label="Mobile" outlined rounded />
      </div>
      <div class="col-6">
        <q-input
          class="q-mx-lg"
          v-model="password"
          label="Password"
          outlined
          rounded
          :type="isPwd ? 'password' : 'text'"
        >
          <template v-slot:append>
            <q-icon
              :name="isPwd ? 'visibility_off' : 'visibility'"
              class="cursor-pointer"
              @click="isPwd = !isPwd"
            />
          </template>
        </q-input>
      </div>
      <q-btn @click="login" class="q-ma-md" color="blue-8">Login</q-btn>
    </div>
  </q-page>
</template>

<script setup>
import { Notify } from 'quasar'
import { api } from 'src/boot/axios'
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const isPwd = ref(true)
const router = useRouter()
const mobile = ref('')
const password = ref('')

function login() {
  api
    .post('oauth/token', {
      grant_type: 'password',
      client_id: 2,
      client_secret: 'kw8FqSlfDcf5dJJ03FOBVu0E2OBqnXkQqycVNLln',
      username: mobile.value,
      password: password.value,
      scope: '*',
    })
    .then((r) => {
      if (r.data.access_token) {
        localStorage.setItem('access_token', r.data.access_token)
        api.defaults.headers = {
          Authorization: 'Bearer ' + localStorage.getItem('access_token'),
          'Content-Type': 'application/json',
          Accept: 'application/json;charset=UTF-8',
        }
      }
      Notify.create({
        type: 'positive',
        position: 'top',
        message: 'login successful ' + r.data.message,
      })
      router.push('/')
    })
    .catch((e) => {
      console.error(e)
      Notify.create({
        type: 'negative',
        position: 'top',
        message: 'login in cach ' + e,
      })
    })
}
</script>

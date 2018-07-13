<template>
  <div id="app">
    <div class="container">
      <div class="columns" style="margin-top: 100px;">
        <div class="column col-2 centered">
          <!-- Если user - пустой объект, то отображаем кнопку
          входа через Google -->
          <g-signin-button
            v-if="isEmpty(user)"
            :params="googleSignInParams"
            @success="onGoogleSignInSuccess"
            @error="onGoogleSignInError"
          >
            <button class="btn btn-block btn-success">
              Google Signin
            </button>
          </g-signin-button>
          <user-panel v-else :user="user"></user-panel>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

import UserPanel from '@/components/UserPanel'

export default {
  name: 'App',
  components: {
    UserPanel
  },
  data () {
    return {
      user: {},
      googleSignInParams: {
        client_id: '400063280121-3q3i0j63hak4bhrru99b9v1jp377hecv.apps.googleusercontent.com'
      }
    }
  },
  methods: {
    onGoogleSignInSuccess (resp) {
      const token = resp.Zi.access_token
      // После успешного входа через Google,
      // отправляем токен доступа на бэкэнд и получаем взамен
      // пользователя и JWT токен
      // P.S. JWT токен в нашем примере не нужен, поэтому его не сохраняем
      axios.post('https://carsrest.herokuapp.com/auth/google/', {
        access_token: token
      })
        .then(resp => {
          this.user = resp.data.user
        })
        .catch(err => {
          console.log(err.response)
        })
    },
    onGoogleSignInError (error) {
      console.log('OH NOES', error)
    },
    isEmpty (obj) {
      return Object.keys(obj).length === 0
    }
  }
}
</script>

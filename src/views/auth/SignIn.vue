<template>
  <div class="auth-page" style="min-height: 85vh">
    <b-row class="d-flex justify-content-center align-items-center" style="min-height: 85vh">
      <div class="auth-container">
        <div class="text-center mt-2">
          <router-link :to="{ name: 'auth.signup'}" style="font-size: 20px;">Not signed up? Go Register</router-link>
        </div>
        <b-row class="d-flex justify-content-center align-items-center">
          <b-col cols="12">
            <div class="auth-page__signin" @keyup.enter="submitLogin" v-if="this.signin_form">
              <span class="error-message text-center text-danger d-block text-center">{{ this.error }}</span>
              <b-form-group class="m-0 mb-1">
                <b-form-input
                  size="lg"
                  class="mb-2"
                  placeholder="Username"
                  type="text"
                  :state="validateState('username')"
                  v-model="form.username"
                  autofocus
                />
              </b-form-group>
              <b-form-group class="password-group">
                <b-form-input
                  size="lg"
                  class="mb-2"
                  placeholder="Password"
                  :type="this.password_reveal ? 'text' : 'password'"
                  :state="validateState('password')"
                  v-model="form.password"
                />
                <div class="password-reveal" @click="passwordReveal">
                  <b-icon variant="primary" :icon="this.password_reveal ? 'eye-slash' : 'eye'" />
                </div>
              </b-form-group>
              <b-button size="large" variant="dark" class="w-100" type="submit" @click="submitLogin()">Login</b-button>
              <div class="text-center mt-2">
                <router-link :to="{ name: 'auth.signup'}" style="font-size: 20px;">Not signed up? Go Register</router-link>
              </div>
              <router-link class="text-center mt-3 d-block cursor-pointer" :to="{ name: 'auth.forgot' }">Forgot password?</router-link>
            </div>
          </b-col>
        </b-row>
      </div>
    </b-row>
  </div>
</template>
<script>
const {required} = require('vuelidate/lib/validators')
import {mapActions} from 'vuex';

export default {
  data() {
    return {
      forget_sent: false,
      signin_form: true,
      password_reveal: false,
      error: null,
      form: {
        username: '',
        password: ''
      },
    }
  },

  validations: {
    form: {
      username: {required},
      password: {required}
    },
  },

  methods: {
    ...mapActions({
      login: 'auth/LOGIN'
    }),

    passwordReveal() {
      this.password_reveal = !this.password_reveal;
    },

    validateState(name) {
      const {$dirty, $error} = this.$v.form[name];
      return $dirty ? !$error : null;
    },

    submitLogin() {
      this.$v.form.$touch();
      if (this.$v.form.$anyError) {
        return;
      }

      const formData = new FormData();
      formData.append('username', this.form.username);
      formData.append('password', this.form.password);

      this.login(formData).then(() => {
        this.$router.push({name: 'ratings.profile'})
      }).catch(error => {
        this.error = error.response.data.message
      })
    }
  }
}
</script>
<style lang="scss">
.auth-page {
  display: flex;
  justify-items: center;
  flex-direction: column;

  h2 {
    font-family: 'Futura PT', sans-serif;
  }

  a {
    color: #9F6;
  }

  .password-group {
    position: relative;

    .is-valid {
      background-image: unset;
    }
  }

  .password-reveal {
    right: 0;
    top: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 47px;
    height: 100%;
    cursor: pointer;
    position: absolute;

    svg {
      width: 20px;
      height: 20px;
    }
  }

  .error-message {
    height: 30px;
  }

  .cursor-pointer {
    cursor: pointer;
  }

  .auth-container {
    background: #000;
    padding: 25px;
    width: 340px;
    border-radius: 5px;
    margin-bottom: 100px;
  }

  .btn-primary {
    color: #fff;
    background-color: #508f3e;
    border-color: #ffffff;
    outline: 0;
    border: 0;
  }
}
</style>

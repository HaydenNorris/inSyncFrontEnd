<template>
  <div class="purpleBackground">
    <div class="pageContainer">
      <button
        v-show="showSignUpField"
        class="rounded-button back"
        type="button"
        @click="toggleView('login')"
        aria-label="Go back to Login form"
        aria-controls="loginForm"
      >
        Back
      </button>
      <div class="logo">
        <img src="@/images/insyncLogo.png" alt="Logo of inSync" />
      </div>
      <form name="loginForm" @reset="resetFields" @submit.prevent="handleSubmit" id="loginForm" role="form">
        <div class="inputContainer">
          <div class="form-group" v-show="showSignUpField">
            <label for="name">Name:</label>
            <input id="name" type="text" v-model.trim="name" aria-required="true" />
          </div>
          <div class="form-group">
            <label for="email">Email:</label>
            <input
              id="email"
              type="email"
              autocomplete="email"
              v-model.trim="email"
              aria-required="true"
            />
          </div>
          <div class="form-group">
            <label for="password">Password:</label>
            <input
              id="password"
              autocomplete="current-password"
              type="password"
              v-model.trim="password"
              aria-required="true"
            />
          </div>
          <div class="form-group" v-show="showSignUpField">
            <label for="confirm_password">Confirm Password:</label>
            <input
              id="confirm_password"
              autocomplete="new-password"
              type="password"
              v-model.trim="confirm_password"
              aria-required="true"
            />
          </div>
          <message-alert
            :show="showAlert"
            :messageText="alertMessage"
            :messageIcon="alertIcon"
            aria-live="assertive"
          />
        </div>
        <div class="button-container">
          <button
            v-show="!showSignUpField"
            class="rounded-button floating-button1"
            type="button"
            @click="toggleView('signUp')"
            aria-label="Switch to Sign Up form"
            aria-controls="loginForm"
          >
            Sign Up
          </button>
          <button
            class="rounded-button floating-button2"
            type="submit"
          >
            {{ showSignUpField ? 'Sign Up' : 'Login' }}
          </button>
          <button
            v-show="!showSignUpField"
            class="rounded-button floating-button1"
            type="reset"
            aria-label="Reset all form fields"
          >
            Reset
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import messageAlert from '@/components/MessageAlert.vue'

export default {
  components: {
    messageAlert
  },
  async created() {
    this.alertIcon = (await import('@/images/reminderIcon.png')).default
    this.$userStore.clear()
    this.$gameStore.clear()
  },
  data() {
    return {
      name: '',
      email: '',
      password: '',
      confirm_password: '',
      showSignUpField: false,
      showAlert: false,
      alertMessage: '',
      alertIcon: ''
    }
  },
  computed: {
    passwordsMatch() {
      return this.password === this.confirm_password
    },
    isFormValid() {
      if (!this.email.trim() || !this.password.trim()) {
        return false
      }
      if (!this.showSignUpField) {
        return true
      }

      const isValid =
        this.name.trim() &&
        this.email.trim() &&
        this.password.trim() &&
        this.confirm_password.trim() !== ''
      const passwordsMatch = this.passwordsMatch
      return isValid && passwordsMatch
    }
  },
  watch: {
    email() {
      this.checkForAlert()
    },
    password() {
      this.checkForAlert()
      this.checkPasswordMismatch()
    },
    confirm_password() {
      this.checkPasswordMismatch()
    }
  },
  methods: {
    resetFields() {
      this.name = '',
        this.email = '',
        this.password = '',
        this.confirm_password = '',
        this.showAlert = false
    },
    toggleView(view) {
      this.showSignUpField = view === 'signUp'
      this.resetFields()
    },
    async handleSubmit() {
      if (!this.isFormValid) {
        this.$error('Details missing...')
        return
      }
      try {
        this.$loading.yes()
        if (this.showSignUpField) {
          await this.$userStore.signUp(this.name, this.email, this.password, this.confirm_password)
        } else {
          await this.$userStore.login(this.email, this.password)
        }
        this.$success('You are logged in!')
        this.$router.push('/landing')
      } catch (axioserror) {
        this.$badRequest(axioserror)
      } finally {
        this.$loading.no()
      }
    },
    checkForAlert() {
      if (!this.showSignUpField && (!this.email.trim() || !this.password.trim())) {
        this.alertMessage = 'Don’t forget to fill in both email and password.'
        this.showAlert = true
      } else {
        this.showAlert = false
      }
    },
    checkPasswordMismatch() {
      if (!this.passwordsMatch && this.showSignUpField) {
        this.alertMessage = 'Passwords do not match'
        this.showAlert = true
      } else if (this.passwordsMatch) {
        this.showAlert = false
      }
    }
  }
}
</script>

<style scoped>
.inputContainer {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.button-container {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 1.5rem;
  animation: containerMovement 25s infinite ease-in-out;
}

@media (max-width: 600px), (max-height: 376px), (max-height: 720px) {
  .button-container {
    flex-direction: column;
    align-items: center;
    gap: 0.8rem;
    animation: containerMovement 25s infinite ease-in-out;
    margin-top: 1rem;
  }

  .rounded-button {
    padding: 0.75rem 0;
    font-size: 1.25rem;
    font-weight: 600;
    width: 8rem;
  }

  .inputContainer {
    overflow-y: scroll;
    width: 100%;
  }

  form {
    width: 100%;
  }
}

@media (max-height: 376px),
(max-height: 720px) {
  .inputContainer {
    max-height: 383px;
  }
}

@media (max-width: 1024px) and (max-height: 600px) {
  .logo {
    width: 100%;
    text-align: center;
    margin-bottom: 1rem;
  }

  .button-container {
    flex-direction: row;
    align-items: center;
    gap: 0.8rem;
    margin: 1rem;
  }

  .rounded-button {
    padding: 0.5rem 1rem;
    font-size: 1rem;
    font-weight: 600;
    width: 7rem;
  }

  .inputContainer {
    max-height: 300px;
    overflow-y: auto;
    width: 100%;
    padding: 0 1rem;
  }

  form {
    width: 90%;
    margin: auto;
  }
}

@keyframes floating {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-5px);
  }
}

@keyframes floating-outer {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(5px);
  }
}

.floating-button1 {
  animation: floating-outer 5s infinite ease-in-out;
}

.floating-button2 {
  animation: floating 5s infinite ease-in-out;
}
</style>

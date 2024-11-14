<template>
  <div class="container">
    <div class="main">
      <input type="checkbox" id="chk" v-model="isSignup" aria-hidden="true" />

      <!-- Signup Form -->
      <div class="signup">
        <form @submit.prevent="handleRegister">
          <label for="chk" aria-hidden="true">Sign up</label>
          <input 
            v-model="registerForm.username"
            type="text" 
            placeholder="Username"
            required
          >
          <input 
            v-model="registerForm.email"
            type="email" 
            placeholder="Email"
            required
          >
          <input 
            v-model="registerForm.phone"
            type="tel" 
            placeholder="Phone"
            required
          >
          <input 
            v-model="registerForm.password"
            type="password" 
            placeholder="Password"
            required
          >
          <button type="submit" :disabled="isLoading">
            {{ isLoading ? 'Loading...' : 'Sign up' }}
          </button>
        </form>
      </div>

      <!-- Login Form -->
      <div class="login">
        <form @submit.prevent="handleLogin">
          <label for="chk" aria-hidden="true">Login</label>
          <input 
            v-model="loginForm.email"
            type="email" 
            placeholder="Email"
            required
          >
          <input 
            v-model="loginForm.password"
            type="password" 
            placeholder="Password"
            required
          >
          <button type="submit" :disabled="isLoading">
            {{ isLoading ? 'Loading...' : 'Login' }}
          </button>
        </form>
      </div>

      <!-- Status Messages -->
      <div v-if="error" class="status-message error">
        {{ error }}
      </div>
      <div v-if="successMessage" class="status-message success">
        {{ successMessage }}
      </div>
    </div>
  </div>
</template>

<script setup>
const isSignup = ref(false)
const isLoading = ref(false)
const error = ref('')
const successMessage = ref('')

// Login form state
const loginForm = ref({
  email: '',
  password: ''
})

// Register form state
const registerForm = ref({
  username: '',
  email: '',
  phone: '',
  password: ''
})

// API base URL
const API_BASE = 'http://localhost/mi_proyecto_php/api.php'

// Login handler
const handleLogin = async () => {
  error.value = ''
  successMessage.value = ''
  isLoading.value = true

  try {
    const response = await fetch(`${API_BASE}?action=login`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(loginForm.value)
    })

    const data = await response.json()

    if (data.success) {
      successMessage.value = 'Login successful'
      localStorage.setItem('userData', JSON.stringify(data.user))
      await new Promise(resolve => setTimeout(resolve, 1000))
      navigateTo('/dashboard')
    } else {
      error.value = data.message || 'Login error'
    }
  } catch (e) {
    error.value = 'Server connection error'
    console.error('Error:', e)
  } finally {
    isLoading.value = false
  }
}

// Register handler
const handleRegister = async () => {
  error.value = ''
  successMessage.value = ''
  isLoading.value = true

  try {
    const response = await fetch(`${API_BASE}?action=register`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(registerForm.value)
    })

    const data = await response.json()

    if (data.success) {
      successMessage.value = 'Registration successful'
      registerForm.value = {
        username: '',
        email: '',
        phone: '',
        password: ''
      }
      setTimeout(() => {
        isSignup.value = false
        successMessage.value = 'Registration successful. Please login.'
      }, 1500)
    } else {
      error.value = data.message || 'Registration error'
    }
  } catch (e) {
    error.value = 'Server connection error'
    console.error('Error:', e)
  } finally {
    isLoading.value = false
  }
}
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: linear-gradient(to bottom, #0f0c29, #302b63, #24243e);
}

.main {
  position: relative;
  width: 350px;
  height: 500px;
  background: #302b63;
  border-radius: 10px;
  box-shadow: 5px 20px 50px #000;
  overflow: hidden;
}



#chk {
  display: none;
}

.signup {
  position: relative;
  width: 100%;
  height: 100%;
  margin-bottom: 25px;
}

label {
  color: #fff;
  font-size: 2.3em;
  justify-content: center;
  display: flex;
  margin: 50px;
  font-weight: bold;
  cursor: pointer;
  transition: 0.5s ease-in-out;
}

input {
  width: 60%;
  height: 20px;
  background: #e0dede;
  justify-content: center;
  display: flex;
  margin: 20px auto;
  padding: 10px;
  border: none;
  outline: none;
  border-radius: 5px;
}

button {
  width: 60%;
  height: 40px;
  margin: 10px auto;
  justify-content: center;
  display: block;
  color: #fff;
  background: #573b8a;
  font-size: 1em;
  font-weight: bold;
  margin-top: 20px;
  outline: none;
  border: none;
  border-radius: 5px;
  transition: 0.2s ease-in;
  cursor: pointer;
}

button:disabled {
  background: #8e8e8e;
  cursor: not-allowed;
}

button:not(:disabled):hover {
  background: #6d44b8;
}

.login {
  height: 460px;
  background: #eee;
  border-radius: 60% / 10%;
  transform: translateY(-170px);
  transition: 0.8s ease-in-out;
}

.login label {
  color: #573b8a;
  transform: scale(0.6);
}

#chk:checked ~ .login {
  transform: translateY(-500px);
}

#chk:checked ~ .login label {
  transform: scale(1);
}

#chk:checked ~ .signup label {
  transform: scale(0.6);
}

.status-message {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  padding: 10px 20px;
  border-radius: 5px;
  font-size: 0.9em;
  z-index: 10;
  white-space: nowrap;
}

.error {
  background: rgba(255, 0, 0, 0.1);
  color: #d32f2f;
}

.success {
  background: rgba(76, 175, 80, 0.1);
  color: #388e3c;
}
</style>
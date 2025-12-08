<template>
  <div class="fill-height d-flex align-center justify-center login-bg">
    <v-card width="420" class="pa-6 login-card" elevation="6">
      <!-- Header with Logo -->
      <div class="text-center mb-6">
        <div class="logo-container mb-3">
          <v-icon size="64" color="primary">mdi-shield-account</v-icon>
        </div>
        <v-card-title class="text-h4 font-weight-bold grey-darken-3">
          Welcome Back
        </v-card-title>
        <v-card-subtitle class="text-subtitle-1 grey--text">
          Sign in to your account
        </v-card-subtitle>
      </div>

      <v-card-text>
        <!-- Email Field -->
        <v-text-field 
          label="Email Address" 
          prepend-inner-icon="mdi-email-outline"
          variant="outlined"
          class="mb-4"
          color="primary"
        ></v-text-field>

        <!-- Password Field -->
        <v-text-field 
          label="Password" 
          prepend-inner-icon="mdi-lock-outline"
          variant="outlined"
          type="password"
          :append-inner-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye'"
          @click:append-inner="showPassword = !showPassword"
          class="mb-2"
          color="primary"
        ></v-text-field>

        <!-- Remember Me & Forgot Password -->
        <div class="d-flex justify-space-between align-center mb-6">
          <v-checkbox
            label="Remember me"
            density="compact"
            color="primary"
          ></v-checkbox>
          <v-btn variant="text" color="primary" size="small">
            Forgot Password?
          </v-btn>
        </div>

        <!-- Login Button -->
        <v-btn 
          color="primary" 
          block 
          size="large"
          @click="login"
          class="login-btn"
        >
          <v-icon left>mdi-login-variant</v-icon>
          Sign In
        </v-btn>

        <!-- Divider -->
        <div class="my-6">
          <v-divider>
            <span class="px-3 text-caption grey--text">OR</span>
          </v-divider>
        </div>

        <!-- Alternative Login -->
        <div class="text-center">
          <v-btn variant="outlined" color="grey" class="mb-2" block>
            <v-icon left>mdi-google</v-icon>
            Continue with Google
          </v-btn>
        </div>
      </v-card-text>

      <!-- Footer -->
      <v-card-actions class="justify-center pt-0">
        <span class="text-caption grey--text">
          Don't have an account? 
          <v-btn variant="text" color="primary" size="small" class="ml-1">
            Sign up
          </v-btn>
        </span>
      </v-card-actions>
    </v-card>

    <!-- Snackbar -->
    <v-snackbar v-model="snackbar" color="success" location="top">
      <v-icon left>mdi-check-circle</v-icon>
      Successfully logged in!
    </v-snackbar>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'

definePageMeta({
  layout: 'login'
})

const router = useRouter()
const snackbar = ref(false)
const showPassword = ref(false)

const login = () => {
  snackbar.value = true
  
  setTimeout(() => {
    router.push('/dashboard')
  }, 1000)
}
</script>

<style scoped>
.login-bg {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  min-height: 100vh;
}

.login-card {
  border-radius: 12px;
  border: 1px solid #e0e0e0;
}

.logo-container {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  width: 80px;
  height: 80px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto;
}

.logo-container .v-icon {
  color: white !important;
}

.login-btn {
  height: 48px;
  font-weight: 600;
  letter-spacing: 0.5px;
}

/* Hover effects */
.v-btn.login-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.3);
  transition: all 0.3s ease;
}

/* Input focus effects */
.v-text-field :deep(.v-field--focused) {
  border-color: #667eea !important;
}
</style>
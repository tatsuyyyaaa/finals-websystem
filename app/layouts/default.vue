<template>
  <v-app>
    <v-app-bar :elevation="2">
      <template v-slot:prepend>
        <v-app-bar-nav-icon @click="toggleSidebar"></v-app-bar-nav-icon>
      </template>

      <v-app-bar-title>Web Management System</v-app-bar-title>
    </v-app-bar>

    <!-- Full Sidebar -->
    <v-navigation-drawer v-model="sidebarOpen" temporary color="blue" location="left">
      <!-- Header -->
      <div class="d-flex align-center pa-4">
        <v-avatar size="40">
          <img src="https://cdn.kami.com.ph/images/1120/0fgjhs39o5t36qk2eg.jpeg?v=1" alt="Avatar">
        </v-avatar>
        <div class="ml-3">
          <div class="font-weight-bold white--text">Admin</div>
          <div class="text-caption white--text">Admin6@gmail.com</div>
        </div>
      </div>

      <v-divider></v-divider>

      <!-- Navigation Links -->
      <v-list density="compact" nav class="pt-2">
        <v-list-item 
          prepend-icon="mdi-view-dashboard" 
          title="Dashboard" 
          value="dashboard" 
          to="/dashboard"
          @click="sidebarOpen = false"
        ></v-list-item>
        <v-list-item 
          prepend-icon="mdi-package-variant" 
          title="Items" 
          value="items" 
          to="/items"
          @click="sidebarOpen = false"
        ></v-list-item>
        <v-list-item 
          prepend-icon="mdi-account-group" 
          title="Members" 
          value="members" 
          to="/members"
          @click="sidebarOpen = false"
        ></v-list-item>
        <v-list-item 
          prepend-icon="mdi-cash-multiple" 
          title="Transactions" 
          value="transactions" 
          to="/transactions"
          @click="sidebarOpen = false"
        ></v-list-item>
      </v-list>

      <v-divider class="my-2"></v-divider>

      <!-- Logout Button Moved Here -->
      <div class="pa-2">
        <v-btn block color="white" variant="outlined" prepend-icon="mdi-logout" @click="logout">
          Logout
        </v-btn>
      </div>
    </v-navigation-drawer>

    <v-main>
      <v-container fluid>
        <slot />
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref } from 'vue'

const sidebarOpen = ref(false)

const toggleSidebar = () => {
  sidebarOpen.value = !sidebarOpen.value
}

const logout = () => {
  window.location.href = '/login'
}
</script>

<style>
/* Make sidebar text white */
.v-navigation-drawer .v-list-item-title,
.v-navigation-drawer .v-list-item-subtitle {
  color: white !important;
}

/* Make icons white */
.v-navigation-drawer .v-icon {
  color: white !important;
}
</style>

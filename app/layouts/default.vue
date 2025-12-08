<template>
  <v-app>
    <v-app-bar :elevation="2">
      <template v-slot:prepend>
        <v-app-bar-nav-icon @click="toggleSidebar"></v-app-bar-nav-icon>
      </template>

      <v-app-bar-title>Web Management System</v-app-bar-title>
      
      <template v-slot:append>
        <v-btn icon="mdi-logout" variant="text" @click="logout" title="Logout"></v-btn>
      </template>
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

      <!-- Minimize button -->
      <template v-slot:append>
        <div class="pa-2">
          <v-btn 
            block 
            variant="outlined" 
            color="white"
            @click="minimizeSidebar"
            prepend-icon="mdi-chevron-left"
          >
            Minimize
          </v-btn>
        </div>
      </template>
    </v-navigation-drawer>

    <!-- Minimized Sidebar - ONLY ICONS -->
    <v-navigation-drawer 
      v-model="sidebarMinimized" 
      temporary 
      color="blue" 
      location="left"
      width="70"
      class="minimized-sidebar"
      v-if="!sidebarOpen && sidebarMinimized"
    >
      <!-- Just icons, no extra elements -->
      <div class="icon-container">
        <v-btn 
          icon 
          to="/dashboard" 
          class="sidebar-icon"
          @click="sidebarMinimized = false"
        >
          <v-icon color="white">mdi-view-dashboard</v-icon>
        </v-btn>
        
        <v-btn 
          icon 
          to="/items" 
          class="sidebar-icon"
          @click="sidebarMinimized = false"
        >
          <v-icon color="white">mdi-package-variant</v-icon>
        </v-btn>
        
        <v-btn 
          icon 
          to="/members" 
          class="sidebar-icon"
          @click="sidebarMinimized = false"
        >
          <v-icon color="white">mdi-account-group</v-icon>
        </v-btn>
        
        <v-btn 
          icon 
          to="/transactions" 
          class="sidebar-icon"
          @click="sidebarMinimized = false"
        >
          <v-icon color="white">mdi-cash-multiple</v-icon>
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
const sidebarMinimized = ref(false)

const toggleSidebar = () => {
  sidebarOpen.value = !sidebarOpen.value
  sidebarMinimized.value = false
}

const minimizeSidebar = () => {
  sidebarOpen.value = false
  sidebarMinimized.value = true
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

/* Minimized sidebar - ONLY ICONS */
.minimized-sidebar {
  display: flex;
  align-items: center;
  justify-content: center;
}

.minimized-sidebar .icon-container {
  display: flex;
  flex-direction: column;
  gap: 24px; /* Space between icons */
}

.minimized-sidebar .sidebar-icon {
  width: 40px;
  height: 40px;
}

.minimized-sidebar .sidebar-icon .v-icon {
  font-size: 24px;
}

/* App content margin when sidebar is minimized */
.v-main {
  transition: margin-left 0.3s;
}

.v-main:has(.minimized-sidebar) {
  margin-left: 70px;
}
</style>
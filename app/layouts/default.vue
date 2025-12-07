<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" color="blue">
      <!-- Header with close button INSIDE sidebar -->
      <div class="d-flex align-center pa-4">
        <v-btn icon @click="drawer = false" class="mr-2">
          <v-icon>mdi-menu</v-icon>
        </v-btn>
        <v-avatar size="40">
          <img src="https://cdn.kami.com.ph/images/1120/0fgjhs39o5t36qk2eg.jpeg?v=1" alt="Avatar">
        </v-avatar>
        <div class="ml-3">
          <div class="font-weight-bold white--text">Mark Manansala</div>
          <div class="text-caption white--text">mmanansala266@gmail.com</div>
        </div>
      </div>

      <v-divider></v-divider>

      <v-list density="compact" nav class="pt-2">
        <v-list-item prepend-icon="mdi-view-dashboard" title="Dashboard" value="dashboard" to="/dashboard"></v-list-item>
        <v-list-item prepend-icon="mdi-package-variant" title="Items" value="items" to="/items"></v-list-item>
        <v-list-item prepend-icon="mdi-account-group" title="Members" value="members" to="/members"></v-list-item>
        <v-list-item prepend-icon="mdi-cash-multiple" title="Transactions" value="transactions" to="/transactions"></v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar :elevation="2">
      <template v-slot:prepend>
        <!-- This OPENS the sidebar -->
        <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>
      </template>

      <v-app-bar-title>Web Management System</v-app-bar-title>
      
      <template v-slot:append>
        <v-btn icon="mdi-logout" variant="text" @click="logout" title="Logout"></v-btn>
      </template>
    </v-app-bar>

    <v-main>
      <v-container fluid>
        <slot />
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
const drawer = ref(false) // Start with sidebar CLOSED

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
<template>
  <v-app class="app">
    <header class="header">
      <v-card class="header__content ma-3 mb-6" title="Portail MATY" elevation="4">
        <template v-slot:prepend>
          <v-img src='/logos-apps/matycom.png' :width="40" contain></v-img>
        </template>
      </v-card>
    </header>
    <v-main class="mx-3">
      <v-row>
        <v-col cols="1" class="d-flex justify-center">
          <v-avatar color="surface" size="large">{{ user.name.split(' ')[0][0] + user.name.split(' ')[1][0]
            }}</v-avatar>
        </v-col>
        <v-col cols="11">
          <router-view />
        </v-col>
      </v-row>
    </v-main>
  </v-app>
</template>

<script setup>
  import { reactive } from "vue";
  const user = reactive({
    name: "Thomas LAMBOLEZ",
  })
</script>

<style lang="scss"></style>
<template>
  <v-row>
    <v-col cols="6">
      <v-text-field label="Rechercher une fonctionnalité ou une application..." density="comfortable" variant="solo"
        v-model="search" hide-details single-line prepend-inner-icon="mdi-magnify">
      </v-text-field>
      <div class="mt-6" v-if="favoriteApplications.length > 0">
        <application-pannel title="Vos applications favorites" :applications="favoriteApplications"
          @eventAddAppToFavs="addApplicationToFavorites" @eventRemoveAppFromFavs="removeApplicationFromFavorites"
          @eventShowSelectedAppModal="showApplicationModal" />
      </div>
      <div class="mt-6">
        <application-pannel title="Toutes vos applications" :applications="filteredApplications"
          @eventAddAppToFavs="addApplicationToFavorites" @eventRemoveAppFromFavs="removeApplicationFromFavorites"
          @eventShowSelectedAppModal="showApplicationModal" />
      </div>
    </v-col>
    <v-col cols="6">
      <p class="text-h6">Sommaire des services</p>
      <v-data-table :items="services.items" :headers="headers" items-per-page="10" :search="search">
        <template v-slot:item.access="{ item }">
          <v-btn :href="item.url" size="small" icon="mdi-open-in-new"></v-btn>
        </template>
      </v-data-table>
    </v-col>
  </v-row>
  <v-dialog v-model="showModal" max-width="600" v-if="selectedApp.obj">
    <v-card>
      <v-card-title>{{ selectedApp.obj.name }}</v-card-title>
      <v-card-text class="d-flex flex-column align-center">
        <p class="mb-4">{{ selectedApp.obj.description }}</p>
        <v-btn color="light" variant="outlined">Visiter {{ selectedApp.obj.name }}</v-btn>
        <slot v-if="selectedApp.obj.services !== undefined">
          <v-divider class="border-opacity-100 w-100 my-4" color="light"></v-divider>
          <v-list class="w-100 " lines="two">
            <v-list-item v-for="(service, index) in selectedApp.obj.services" :key="index" :title="service.name"
              :subtitle="service.description">

              <template v-slot:append>
                <v-btn icon="mdi-open-in-new" size="x-small" color="light"></v-btn>
              </template>
            </v-list-item>
          </v-list>
        </slot>
      </v-card-text>
      <v-card-actions>
        <v-btn @click="showModal = false">Fermer</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script setup>
  import applicationsData from "../assets/applications";
  import ApplicationPannel from "@/components/ApplicationPannel.vue";
  import { ref, reactive, onMounted, computed } from "vue";

  const applications = reactive({ data: applicationsData });
  const headers = [
    { title: "Nom du service", value: "name" },
    { title: "Nom de l'application", value: "application" },
    { title: "", value: item => `Accéder au ${item.application}`, key: "access" }
  ]

  let lsFavoriteApplications = localStorage.getItem('favoriteApplications') || "[]";
  let favoriteApplications = reactive(JSON.parse(lsFavoriteApplications));

  const search = ref('')
  const services = reactive({ items: [] });
  const showModal = ref(false);
  const selectedApp = reactive({ obj: { name: '', description: '', services: [] } });

  onMounted(() => {
    favoriteApplications.forEach((app) => {
      applications.data.find(el => el.name == app.name).isFavorite = true;
    })

    applications.data.forEach((app) => {
      if (app.services && app.services.length > 0) {
        app.services.forEach((service) => {
          service.application = app.name;
          service.url = app.url;
          services.items.push({ ...service });
        })
      }
    })
  })

  function addApplicationToFavorites(application) {
    application.isFavorite = true;
    favoriteApplications.push(application);
    lsFavoriteApplications = JSON.stringify(favoriteApplications);
    localStorage.setItem("favoriteApplications", lsFavoriteApplications);
  }

  function removeApplicationFromFavorites(application) {
    application.isFavorite = false;
    const index = favoriteApplications.findIndex(el => el.name == application.name);
    favoriteApplications.splice(index, 1);
    lsFavoriteApplications = JSON.stringify(favoriteApplications);
    localStorage.setItem("favoriteApplications", lsFavoriteApplications);

    applications.find(el => el.name == application.name).isFavorite = false;
  }

  function showApplicationModal(application) {
    console.log(application);
    selectedApp.obj = { ...application }
    console.log(selectedApp);
    showModal.value = true;
  }

  const filteredApplications = computed(() => {
    const s = search.value.toLowerCase();
    return applications.data.filter(app => app.name.toLowerCase().includes(s));
  })
</script>


<style lang="scss">
  .application-card {
    height: 100%;
    line-height: normal;

    &.-hover {
      background-color: #ffffffa2;
    }

    .card-content {
      height: 100%;

      .application-name {
        height: 100%;
      }

      .application-image {
        .v-img__img {
          object-fit: contain;
        }
      }
    }
  }
</style>
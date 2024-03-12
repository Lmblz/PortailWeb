<template>
    <p class="text-h6">{{ title }}</p>
    <v-card class="pa-3">
        <v-row>
            <v-col cols="3" lg="3" md="4" s="6" v-for="(application, index) in applications" :key="index">
                <v-hover v-slot="{ isHovering, props }">
                    <v-card color="light" class="pa-3 d-flex flex-column align-center application-card"
                        :class="{ '-hover': isHovering }" v-bind="props">
                        <div class="card-content d-flex flex-column align-center justify-space-between">
                            <b class="text-center mb-3 application-name d-flex align-center">{{ application.name }}</b>
                            <v-avatar size="small">
                                <v-img class="application-image" :src="'/logos-apps/' + application.logo"
                                    contain></v-img>
                            </v-avatar>
                        </div>
                        <v-overlay :model-value="isHovering" class="d-flex align-center justify-center" scrim="primary"
                            contained opacity=".6">
                            <v-btn icon="mdi-information" size="x-small" variant="flat" color="surface"
                                @click="$emit('eventShowSelectedAppModal', application)"></v-btn>
                            <v-btn icon="mdi-open-in-new" size="x-small" variant="flat" color="surface" class="mx-2"
                                :href="application.url"></v-btn>
                            <v-btn :icon="application.isFavorite ? 'mdi-star' : 'mdi-star-outline'" size="x-small"
                                variant="flat" color="surface"
                                @click="!application.isFavorite ? $emit('eventAddAppToFavs', application) : $emit('eventRemoveAppFromFavs', application)"></v-btn>
                        </v-overlay>
                    </v-card>
                </v-hover>
            </v-col>
        </v-row>
    </v-card>
</template>

<script setup>
    defineProps({
        applications: Array,
        title: String,
    })

    defineEmits([
        'eventAddAppToFavs',
        'eventRemoveAppFromFavs',
        'eventShowSelectedAppModal',
    ])
</script>
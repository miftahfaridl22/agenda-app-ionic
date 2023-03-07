<template>
    <ion-page>
        <ion-header :translucent="true">
            <ion-toolbar color="primary">
                <ion-title>
                    Cari Agenda
                </ion-title>
            </ion-toolbar>
        </ion-header>
        <ion-content>
            
        </ion-content>
    </ion-page>
</template>

<script lang="ts">
    import {
        IonPage,
        IonHeader,
        IonToolbar,
        IonTitle,
        IonContent
    } from '@ionic/vue';
    import { defineComponent } from 'vue';

    export default defineComponent({
        name: 'CariagendaPage',
        components: {
            IonPage,
            IonHeader,
            IonToolbar,
            IonTitle,
            IonContent
        },   
    });
</script>

<style scoped>

</style>
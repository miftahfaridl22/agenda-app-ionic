<template>
    <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Sign Up</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content>
        <div>
            <ion-item>
                <ion-label position="floating">NIP</ion-label>
                <ion-input type="text" v-model="nip"></ion-input>
            </ion-item>
            <ion-item>
                <ion-label position="floating">Nama</ion-label>
                <ion-input type="text" v-model="nama"></ion-input>
            </ion-item>
            <ion-item>
                <ion-label position="floating">Password</ion-label>
                <ion-input type="password" v-model="password"></ion-input>
            </ion-item>
            <ion-button shape="round" expand="full" @click="register">Submit</ion-button>
        </div>
    </ion-content>
    </ion-page>
</template>

<script lang="ts">

    import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonItem, IonInput, IonLabel, IonButton} from '@ionic/vue';
    import { defineComponent } from 'vue';
    import axios from 'axios';
    
    export default defineComponent({
        components: {
            IonContent,
            IonHeader,
            IonPage,
            IonTitle,
            IonToolbar,
            IonItem,
            IonInput,
            IonLabel,
            IonButton
        },
        data() {
            return {
                nip: '',
                nama: '',
                password: ''
            }
        },
        methods: {
            register(){
                axios.post("", null, {params: {"nip" : this.nip, "nama": this.nama, "password": this.password}}).
                then((res => {
                    alert(res.data);
                })).
                catch(
                    function(error){
                        alert(error);
                    }
                );
            }
        }
    });
</script>

<style scoped>

</style>
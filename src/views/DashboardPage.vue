<template>
    <ion-page>
        <ion-header :translucent="true">
            <ion-toolbar color="primary">
                <ion-buttons slot="primary">
                    <ion-button @click="logout">
                        <ion-icon :icon="logOut" size="large"></ion-icon>
                    </ion-button>
                </ion-buttons>
                <ion-title>Agenda BPPKAD</ion-title>
            </ion-toolbar>
            <ion-toolbar>
                <ion-item lines="full">
                    <ion-icon :icon="personCircle" slot="start" color="primary"></ion-icon>
                    <ion-label>{{user.nama_lengkap}}</ion-label>
                </ion-item>
            </ion-toolbar>
            <ion-toolbar>
                <ion-item lines="full">
                    <ion-icon :icon="medal" slot="start" color="primary"></ion-icon>
                    <div class="ion-text-wrap">
                        {{user.jabatan}}
                    </div>
                </ion-item>
            </ion-toolbar>
        </ion-header>

        <ion-content :fullscreen="true">
            <!-- <div v-for="item in agenda" :key="item.id">
                <ion-card color="warning">
                    <ion-card-header>
                        <ion-card-title style="font-size:18px">{{item.nama_agenda}}</ion-card-title>
                    </ion-card-header>
                    <ion-card-content>
                        <p style="font-size:16px; font-weight:bold">
                            <ion-icon :icon="alarm" size="medium"></ion-icon>
                            {{item.waktu_agenda}} WIB
                        </p>
                        <p style="font-size:15px; font-weight:bold">
                            <ion-icon :icon="location" size="medium"></ion-icon>
                            {{item.tempat_agenda}}
                        </p>
                        <p style="font-size:15px;">
                            <ion-icon :icon="peopleCircle" size="medium"></ion-icon>
                            {{item.disposisi_agenda}}
                        </p>
                    </ion-card-content>
                </ion-card>
            </div> -->
            <div v-if="segment == 'today'">
                <div v-for="item in agenda" :key="item.id">
                    <ion-card color="warning">
                        <ion-card-header>
                            <ion-card-title style="font-size:18px">{{item.nama_agenda}}</ion-card-title>
                        </ion-card-header>
                        <ion-card-content>
                            <p style="font-size:16px; font-weight:bold">
                                <ion-icon :icon="alarm" size="medium"></ion-icon>
                                {{item.waktu_agenda}} WIB
                            </p>
                            <p style="font-size:15px; font-weight:bold">
                                <ion-icon :icon="location" size="medium"></ion-icon>
                                {{item.tempat_agenda}}
                            </p>
                            <p style="font-size:15px;">
                                <ion-icon :icon="peopleCircle" size="medium"></ion-icon>
                                {{item.disposisi_agenda}}
                            </p>
                        </ion-card-content>
                    </ion-card>
                </div>
            </div>

            <div v-if="segment == 'tomorrow'">
                <div v-for="itembsk in agendabesok" :key="itembsk.id">
                    <ion-card color="warning">
                        <ion-card-header>
                            <ion-card-title style="font-size:18px">{{itembsk.nama_agenda}}</ion-card-title>
                        </ion-card-header>
                        <ion-card-content>
                            <p style="font-size:16px; font-weight:bold">
                                <ion-icon :icon="alarm" size="medium"></ion-icon>
                                {{itembsk.waktu_agenda}} WIB
                            </p>
                            <p style="font-size:15px; font-weight:bold">
                                <ion-icon :icon="location" size="medium"></ion-icon>
                                {{itembsk.tempat_agenda}}
                            </p>
                            <p style="font-size:15px;">
                                <ion-icon :icon="peopleCircle" size="medium"></ion-icon>
                                {{itembsk.disposisi_agenda}}
                            </p>
                        </ion-card-content>
                    </ion-card>
                </div>
            </div>
        </ion-content>
            <ion-toolbar>
                <ion-segment @ionChange="segmentChanged($event)" v-model="segment">
                    <ion-segment-button value="today">
                        <ion-label>Hari ini</ion-label>
                        <ion-label style="font-size:11px">{{hari}}</ion-label>
                    </ion-segment-button>
                    <ion-segment-button value="tomorrow">
                        <ion-label>Besok</ion-label>
                        <ion-label style="font-size:11px">{{haribesok}}</ion-label>
                    </ion-segment-button>
                </ion-segment>
            </ion-toolbar>
        <ion-footer>

        </ion-footer>
    </ion-page>
</template>

<script lang="ts">
import {
    IonContent, 
    IonHeader, 
    IonPage, 
    IonTitle, 
    IonToolbar, 
    IonButton, 
    IonButtons, 
    IonCard, 
    IonCardContent,
    IonCardHeader, 
    IonCardTitle,
    IonIcon,
    IonItem,
    IonLabel,
    IonSegment,
    IonSegmentButton,
    IonFooter,
    useIonRouter
    } from '@ionic/vue';
import { defineComponent, onMounted, ref } from 'vue';
import axios from 'axios';
import dayjs from 'dayjs';
import 'dayjs/locale/id';
import { alarm, location, peopleCircle, logOut, personCircle, calendar, medal } from 'ionicons/icons'

export default defineComponent({
    name: 'DashboardPage',
    components: {
        IonContent, 
        IonHeader, 
        IonPage, 
        IonTitle, 
        IonToolbar,
        IonButton,
        IonButtons,
        IonCard, 
        IonCardContent,
        IonCardHeader, 
        IonCardTitle,
        IonIcon,
        IonItem,
        IonLabel,
        IonSegment,
        IonSegmentButton,
        IonFooter
    },
    setup(){
        const token = localStorage.getItem('acc_token');
        const ionRouter = useIonRouter();
        const user = ref('');
        const agenda = ref([]);
        const agendabesok = ref([]);
        const hari = ref('');
        const haribesok = ref('');
        const segment = ref('today');

        function segmentChanged(ev: CustomEvent) {
            segment.value = ev.detail.value;
        }

        onMounted(() => {

            if(!token){
                return ionRouter.navigate('/home', 'forward', 'replace');
            }

            // get data
            axios.defaults.headers.common.Authorization = `Bearer ${token}`;
            //axios.get('https://mflabdev.my.id/api/user')
            axios.get('http://localhost:8000/api/user')
            .then(res => {
                console.log(res);
                user.value = res.data;
            })
            .catch(error => {
                console.log(error);
            });

            getagenda();
            getagendabesok();

        });

        // get Agenda hari ini
        function getagenda()
        {
            axios.defaults.headers.common.Authorization = `Bearer ${token}`;
            //axios.get('https://mflabdev.my.id/api/tampilagenda')
            axios.get('http://localhost:8000/api/tampilagenda')
            .then(res => {
                console.log(res.data.data);
                agenda.value = res.data.data;
                hari.value = dayjs(res.data.data[0].tanggal_agenda).format("DD MMMM YYYY");
            })
            .catch(error => {
                console.log(error);
            });
        }


        // get Agenda besok
        function getagendabesok()
        {
            axios.defaults.headers.common.Authorization = `Bearer ${token}`;
            //axios.get('https://mflabdev.my.id/api/tampilagendabesok')
            axios.get('http://localhost:8000/api/tampilagendabesok')
            .then(res => {
                console.log(res.data.data);
                agendabesok.value = res.data.data;
                haribesok.value = dayjs(res.data.data[0].tanggal_agenda).format("DD MMMM YYYY");
            })
            .catch(error => {
                console.log(error);
            });
        }


        // logout
        function logout()
        {
            axios.defaults.headers.common.Authorization = `Bearer ${token}`;
            //axios.post('https://mflabdev.my.id/api/logout')
            axios.post('http://localhost:8000/api/logout')
            .then(res => {
                if(res.data.success){
                    
                    //remove local Storage
                    localStorage.removeItem('acc_token');

                    return ionRouter.navigate('/home', 'forward', 'replace');
                }
            }).catch(error => {
                console.log('error');
            })
        }

        return {
            token, 
            ionRouter, 
            user, 
            logout, 
            getagenda,
            getagendabesok, 
            agenda,
            agendabesok, 
            hari,
            haribesok, 
            alarm, 
            location, 
            peopleCircle,
            logOut,
            personCircle,
            calendar,
            segment,
            segmentChanged,
            medal,
        }
    }
});
</script>
<template>
  <ion-page>
    <ion-content :fullscreen="true" color="primary">
      <div id="container">
        <ion-text style="font-size: 40px;">
          Agenda
        </ion-text>
        <ion-card style="margin-top:40px">
          <ion-item style="margin-left:8px; margin-right:8px;">
            <ion-icon :src="person"></ion-icon>&nbsp;
            <ion-input type="text" placeholder="NIP" v-model="user.nip"></ion-input>
          </ion-item>

          <ion-item style="margin-left:8px; margin-right:8px;">
            <ion-icon :src="lockClosed"></ion-icon>&nbsp;
            <ion-input type="password" placeholder="Password" v-model="user.password"></ion-input>
          </ion-item>

          <ion-button :disabled="disabled" shape="round" expand="full" @click="login" color="primary" style="margin-right:8px; margin-left:8px; margin-top:20px; margin-bottom: 20px;">Login</ion-button>
        </ion-card>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonContent, IonPage, IonItem, IonInput, IonButton, IonIcon, IonCard, IonText, toastController, useIonRouter, useBackButton } from '@ionic/vue';
import { defineComponent, reactive, ref, computed, onMounted } from 'vue';
import { person, lockClosed } from 'ionicons/icons';
import axios from 'axios';
import { alertCircle } from 'ionicons/icons';
import { App } from '@capacitor/app';


export default defineComponent({
  name: 'HomePage',
  components: {
    IonContent,
    IonPage,
    IonItem,
    IonInput,
    IonButton,
    IonIcon,
    IonCard,
    IonText
  },
  setup(){
    
    const ionRouter = useIonRouter();

    const user = reactive({
      nip: '',
      password: ''
    });

    onMounted(() => {
      const nipawal = ref();
      nipawal.value = localStorage.getItem('nip');  

      if(nipawal.value){
        user.nip = nipawal.value;
      } else {
        user.nip = '';
      }
    });

    const loginGagal = ref();

    // jika nip atau password tidak diisi maka tombol login tidak aktif
    const disabled = computed(() => !user.nip || !user.password);


    useBackButton(-1, () => {
      if(!ionRouter.canGoBack()) {
        App.exitApp();
      }
    });


    async function presentToast(message: any)
    {
      const toast = await toastController.create({
        message: message,
        duration: 2000,
        position: 'bottom',
        cssClass: 'toast-custom-class',
        icon: alertCircle,
        color: 'danger',
        buttons: [
            {
              text: 'Dismiss',
              role: 'cancel'
            }
        ],
      });

      await toast.present();
    }

    function login(){
      
      let nip = user.nip;
      let password = user.password;

      //axios.defaults.withCredentials = true;

      //axios.post('https://mflabdev.my.id/api/login',{
      axios.post('http://localhost:8000/api/login',{
        nip, 
        password,
      }).
      then(response => {

        //console.log(response);
        if(response.data.success){

          // set token
          localStorage.setItem('acc_token', response.data.access_token);
          localStorage.setItem('nip', response.data.nip);
          
          return ionRouter.navigate('/dashboard', 'forward', 'replace');
          
        } 

        clearData();

      }).catch(error => {
        presentToast(error.response.data.message);
      })
    }

    const clearData = () => {
      user.password = '';
    }

    return {
      person, lockClosed, user, login, loginGagal, presentToast, disabled, alertCircle, ionRouter, clearData
    }
  }
});
</script>

<style scoped>
#container {
  text-align: center;
  
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  
  color: #8c8c8c;
  
  margin: 0;
}

#container a {
  text-decoration: none;
}

ion-avatar {
  --border-radius: 5px;
  margin: 0 auto;
  width: 100;
  height: 100;
}


.toast-custom-class {
    --color:#ffffff;
    --border-color:#000000;
    --border-radius:3px;
    --border-style:solid;
    --border-width:3px;    
    --background:#1833FF;
    --button-color:#ffffff;
}


</style>

<template>
  <ion-page>
    <toolbar-reutilizable-component :title="'Inicio'"/>
    <ion-content>
      <ion-card class="welcome-card">
        <ion-icon :icon="personCircleOutline" class="welcome-icon" />
        <ion-card-title>Bienvenido {{ TypeUser }} {{ UserName }} </ion-card-title>
        <ion-card-content>
          <p class="welcome-text">Nos alegra verte</p>
          <p class="welcome-text">Esperamos que la aplicación te brinde una experiencia positiva a la hora de realizar tu trabajo</p>
        </ion-card-content>
      </ion-card>

      <ion-card class="control-card">
        <ion-card-header>
          <ion-icon :icon="timeOutline" slot="start" class="card-icon" />
          <ion-card-title>Control Horario</ion-card-title>
          <ion-card-subtitle>Registro de entradas y salidas</ion-card-subtitle>
        </ion-card-header>
        <ion-card-content>
          <p class="detailed-description">Utilice el escáner de códigos QR para registrar su entrada y salida de manera eficiente. Este sistema le permite llevar un control preciso de sus horarios de trabajo, garantizando que todos los registros sean exactos y accesibles en tiempo real. Recuerde escanear al entrar y al salir para mantener su horario actualizado.</p>
          <ion-icon :icon="calendarOutline" class="control-icon" />
        </ion-card-content>
      </ion-card>

    </ion-content>
  </ion-page>
</template>

<script>
import { IonPage, IonContent, IonCard, IonCardHeader, IonCardSubtitle, IonCardTitle, IonCardContent, IonIcon } from '@ionic/vue';
import ToolbarReutilizableComponent from '../components/ToolbarReutilizableComponent.vue';
import { timeOutline, personCircleOutline, calendarOutline } from 'ionicons/icons';

export default {
  name: 'HomeAdComponent',
  components: {
    IonPage,
    IonContent,
    IonCard,
    IonCardHeader,
    IonCardSubtitle,
    IonCardTitle, 
    IonCardContent,
    IonIcon,
    ToolbarReutilizableComponent
  },
  data() {
    return {  
      UserName: "",
      TypeUser: ""
    }
  },
  setup() {
    return {
      timeOutline,
      personCircleOutline,
      calendarOutline
    }
  },
  methods: {
    getNameUserLogin() {
      try {
        const User = localStorage.getItem('User-login');
        if (User) {
          const NameUser = JSON.parse(User);
          this.UserName = NameUser.nombre; 
          this.idUser = NameUser.id_usu_tipo; 
          if (NameUser.id_usu_tipo == 2) {
            this.TypeUser = "EMPLEADO"; 
          }
        } else {
          window.location.href = '/login'; 
        }   
      } catch {
        window.location.href = '/login'; 
      }
    }
  },
  mounted() {
    this.getNameUserLogin();
  }
}
</script>

<style>
.welcome-card {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  margin: 20px;
  padding: 20px;
  background-color: #f0f8ff;
  border-radius: 10px;
}

.welcome-icon {
  font-size: 50px;
  color: #4CAF50;
  margin-bottom: 10px;
}

.welcome-text {
  font-size: 16px;
  margin-top: 10px;
  color: #555;
}

.control-card {
  margin: 20px;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 10px;
}

.card-icon {
  font-size: 30px;
  color: #007BFF;
  margin-right: 10px;
}

.detailed-description {
  font-size: 16px;
  margin-top: 10px;
  color: #555;
}

.control-icon {
  display: block;
  font-size: 50px;
  color: #007BFF;
  margin: 20px auto;
}
</style>

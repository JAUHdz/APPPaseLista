<template>
  <ion-page>
    <toolbar-reutilizable-component :title="'Inicio'"/>
    <ion-content>

      <ion-card class="welcome-card">
        <ion-card-header>
          <ion-card-title>Bienvenido administrador {{ UserName }}</ion-card-title>
          <ion-card-subtitle>Nos alegra verte</ion-card-subtitle>
        </ion-card-header>
        <ion-card-content class="center-content">
          <ion-icon :icon="watchOutline" class="welcome-icon"></ion-icon>
          <p class="welcome-text">¿Qué le gustaría hacer hoy?</p>
        </ion-card-content>
      </ion-card>

      <ion-card class="action-card">
        <ion-card-header @click="rutausuarios">
          <ion-icon :icon="personAddOutline" class="card-icon" />
          <ion-card-title>Manejo y administración de usuarios</ion-card-title>
          <ion-card-subtitle>Administra y gestiona la información de los usuarios de manera eficiente</ion-card-subtitle>
        </ion-card-header>
        <ion-card-content>
          <p class="card-text">Esta herramienta le permite crear nuevas cuentas de usuario, consultar usuarios existentes y actualizar la información necesaria para un manejo adecuado del personal.</p>
        </ion-card-content>
      </ion-card>

      <ion-card class="action-card">
        <ion-card-header @click="rutacontrolh">
          <ion-icon :icon="listOutline" class="card-icon" />
          <ion-card-title>Consultar Registros</ion-card-title>
          <ion-card-subtitle>Consulta y verifica los horarios de entrada y salida de los empleados</ion-card-subtitle>
        </ion-card-header>
        <ion-card-content>
          <p class="card-text">Acceda a los registros de horarios para asegurarse de que todos los empleados cumplen con sus jornadas laborales. Esta función permite revisar y validar el control de asistencia de manera precisa y rápida.</p>
        </ion-card-content>
      </ion-card>

    </ion-content>
  </ion-page>
</template>

<script>
import { IonPage, IonContent, IonCard, IonCardHeader, IonCardSubtitle, IonCardTitle, IonCardContent, IonIcon } from '@ionic/vue';
import ToolbarReutilizableComponent from '../components/ToolbarReutilizableComponent.vue';
import { personAddOutline, listOutline, watchOutline } from 'ionicons/icons';

export default {
  name: 'HomeAdminComponent',
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
      UserName: ""
    }
  },
  setup() {
    return {
      personAddOutline,
      listOutline,
      watchOutline
    }
  },
  methods: {
    getNameUserLogin() {
      try {
        const User = localStorage.getItem('User-login');
        if (User) {
          const NameUser = JSON.parse(User);
          this.UserName = NameUser.nombre;  
        } else {
          window.location.href = '/login'; 
        }   
      } catch {
        window.location.href = '/login'; 
      }
    },
    rutacontrolh(){
      this.$router.push("/boardaditivos");
    },
    rutausuarios(){
      this.$router.push("/board");
    },
  },
  mounted() {
    this.getNameUserLogin();
  }
}
</script>

<style>
.welcome-card {
  margin: 20px;
  padding: 20px;
  background-color: #f0f8ff;
  border-radius: 10px;
  text-align: center;
}

.welcome-icon {
  font-size: 90px;
  color: #007BFF;
  margin-bottom: 10px;
}

.welcome-text {
  font-size: 16px;
  color: #555;
}

.center-content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.action-card {
  margin: 20px;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 10px;
}

.card-icon {
  font-size: 50px;
  color: #007BFF;
  margin-right: 10px;
}

.card-text {
  font-size: 16px;
  color: #555;
  margin-top: 10px;
}
</style>

<template>
    <ion-page>
      <toolbar-reutilizable-component :title="'Cemex'"/>
      <ion-content>
        
        <ion-card>
          <ion-card-header class="Center-Logo-Cemex">
              <ion-card-title>Bienvenido administrador {{ UserName }}  </ion-card-title>
              <br>
              <ion-card-subtitle>Nos alegra verte, ¿Qué le gustaría hacer hoy?</ion-card-subtitle>
          </ion-card-header>
          
          <ion-card-content>
            <div class="Center-Logo-Cemex">
              <ion-img src="/Cemex_log.png" style="max-width: 300px; height: auto; margin-bottom: 10px;"></ion-img>
            </div>

              <p style="font-size: 16px; margin-top: 10px; text-align: center; ">Recuerda un gran poder con lleva una gran responsabilidad</p>
          </ion-card-content>
        </ion-card>

        <card-reutilizable-component :title="titleCard1" :subtitle="subtitleCard1" :RutaOne="'/register'" :RutaDos="'/board'" :textUno="textUnoCard1" :textDos="textDosCard1" :IconoUno="personAddOutline" :IconoDos="listOutline"/>
        <card-reutilizable-component :title="titleCard2" :subtitle="subtitleCard2" :RutaOne="'/boardaditivos'" :textUno="textUnoCard2" :IconoUno="listOutline"/>
        <card-reutilizable-component :title="titleCard3" :subtitle="subtitleCard3" :textUno="textUnoCard3" :textDos="textDosCard3" :IconoUno="lockClosedOutline" :IconoDos="callOutline"/>

      </ion-content>
    </ion-page>
  </template>
  
  <script>
  import { IonPage, IonContent, IonCard, IonCardHeader, IonCardSubtitle, IonCardTitle, IonCardContent, IonImg } from '@ionic/vue';
  import ToolbarReutilizableComponent from '../components/ToolbarReutilizableComponent.vue'
  import CardReutilizableComponent from '../components/CardReutilizableComponent.vue'
  
  import { personAddOutline, listOutline, lockClosedOutline, callOutline} from 'ionicons/icons';
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
      IonImg,
      CardReutilizableComponent,
      ToolbarReutilizableComponent,
    },
    data() {
      return {
        titleCard1: "Manejo y administracion de usuarios",
        subtitleCard1: "Esta herramienta nos permite administrar mejor la informacion de nuestros usuarios",
        textUnoCard1: "Crear nueva cuenta?",
        textDosCard1: "Consultar usuarios existentes?",

        titleCard2: "Consultar aditivos vaciados o contenedores",
        subtitleCard2: "Consultas sobre aditivos vaciados en contenedores",
        textUnoCard2: "Consultar aditivos vaciados?",
        
        titleCard3: "Gestionar cuenta",
        subtitleCard3: "Administra tu informacion y datos personales para un mejor inicio de sesion",
        textUnoCard3: "Cambiar Contraseña?",
        textDosCard3: "Agregar numero de telefono?",

        UserName:"",
      }
    },
    setup() {
      return {
        personAddOutline,
        listOutline,
        lockClosedOutline,
        callOutline,
      }
    },
    methods: {
      getNameUserLogin (){
        try {
          const User = localStorage.getItem('User-login');
          if (User) {
            const NameUser = JSON.parse(User);
            this.UserName = NameUser.nom_usuario;  
            console.log("Datos obtenidos correctamente");
          } else {
            console.log("Vuelvaa iniciar sesion para evitar cualquier error con la sesion iniciada");
            window.location.href = '/login'; 
          }   
        } catch {
          console.log("Hubo un error al obtener los datos de la sesion");
          window.location.href = '/login'; 
        }
      }
    },
    mounted() {
      this.getNameUserLogin();
    },
    

  }
  </script>

<style>

.Center-Logo-Cemex{
  display: flex;
  justify-content: center;
  align-items: center;
}

</style>
  
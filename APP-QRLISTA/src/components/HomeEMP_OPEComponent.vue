<template>
    <ion-page>
      <toolbar-reutilizable-component :title="'Cemex'"/>
      <ion-content>
       
        <div style="background-color: red; color:red;">.</div>
       <ion-card class="cont-center">
              <ion-card-title style="margin-top: 10px;">Bienvenido {{ TypeUser }} {{ UserName }} </ion-card-title>
              <h5>Nos alegra verte</h5>
              <ion-img src="/Cemex_log.png" style="max-width: 250px; height: auto; margin-bottom: 10px; margin-top: 10px;"></ion-img>
         <ion-card-content>
            <p style="font-size: 16px; margin-top: 10px;">Puede comenzar a realizar las validaciones con el escáner de codigos QR</p>
            <p style="font-size: 16px; margin-top: 10px;">Esperamos la aplicacion de una experencia positiva a la hora de realizar el trabajo</p>
            </ion-card-content>
        </ion-card>
        <div style="background-color: blue; color:blue;">.</div>
   
        <ion-card>
          <ion-card-header>
            <ion-card-title>Escáner</ion-card-title>
            <ion-card-subtitle>Validación de vaciado de aditivos en tanqués</ion-card-subtitle>
          </ion-card-header>
          <ion-card-content>
            <p>La validación se realiza seleccionando la parte a escanear ya sea el aditivo  o el tanque, la aplicación mostrara en una tabla si la validacion es correcta con un color azul o incorrecta con un color rojo</p>
          </ion-card-content>
        </ion-card>

        <card-reutilizable-component :title="titleCard3" :subtitle="subtitleCard3" :textUno="textUnoCard3" :textDos="textDosCard3" :IconoUno="lockClosedOutline" :IconoDos="callOutline"/>

      </ion-content>
    </ion-page>
  </template>
  
  <script>
  import { IonPage, IonContent, IonCard, IonCardHeader, IonCardSubtitle, IonCardTitle, IonCardContent, IonImg} from '@ionic/vue';
  import ToolbarReutilizableComponent from '../components/ToolbarReutilizableComponent.vue'
  import CardReutilizableComponent from '../components/CardReutilizableComponent.vue'
  
  import {lockClosedOutline, callOutline} from 'ionicons/icons';
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
      ToolbarReutilizableComponent,
      CardReutilizableComponent
    },
    data() {
      return {  
        titleCard3: "Gestionar cuenta",
        subtitleCard3: "Administra tu informacion y datos personales para un mejor inicio de sesion",
        textUnoCard3: "Cambiar Contraseña?",
        textDosCard3: "Agregar numero de telefono?",

        UserName:"",
        TypeUser:"",
      }
    },
    setup() {
      return {
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
            this.idUser= NameUser.idusucattipousuario; 
                if(NameUser.idusucattipousuario==2){
                    this.TypeUser= "EMPLEADO"; 
                }else if(NameUser.idusucattipousuario==3){
                    this.TypeUser= "OPERADOR";
                }
           // console.log("Datos obtenidos correctamente");
          } else {
            //console.log("Vuelva iniciar sesion para evitar cualquier error con la sesion iniciada");
            window.location.href = '/login'; 
          }   
        } catch {
         // console.log("Hubo un error al obtener los datos de la sesion");
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
    .cont-center{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 50%;
}

  </style>
  
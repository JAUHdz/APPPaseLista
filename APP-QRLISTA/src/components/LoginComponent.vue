<template>
    <ion-page>

      <ion-content>
          <div class="centrado content">
              <div class="margen100" >
                <ion-img src="/User2.png" class="image-user"></ion-img>
            </div>
          
            <div class="inputs">
                <ion-label >Username</ion-label>
                  <ion-input class="item-inputs" v-model="username" fill="outline" placeholder="Ej: Admin"></ion-input>
                  <ion-label>Password</ion-label>
                  <ion-input class="item-inputs" v-model="password"   type="password" fill="outline" placeholder="Ej: ashnoias">
                    <ion-input-password-toggle slot="end" ></ion-input-password-toggle>
                  </ion-input>
            </div>
            <div style="margin: 20px;">
              <ion-button id="open-loading" @click="login" fill="solid" class="login-button">Login</ion-button>
              <ion-loading :is-open="loading" message="Cargando..." spinner="circles"></ion-loading>
            </div>
        <footer class="footer">
          <div class="imagefooter">
              <img src="/FLogin.png" alt="Descripción de la imagen" class="full-width-image">
          </div>
      </footer>
          </div>
        </ion-content>
    </ion-page>
  </template>
  
  <script >
  import {IonPage,IonInput, IonButton, IonImg,IonContent, IonLabel, IonInputPasswordToggle, IonLoading} from '@ionic/vue';
  import { Geolocation } from '@ionic-native/geolocation';
  
  export default {
    name:'LoginComponente',
    components:{
      IonPage,
      IonInput, 
      IonButton, 
      IonImg,
      IonContent,
      IonLabel,
      IonInputPasswordToggle,
      IonLoading
    },

    data() {
    return {
      username: '',
      password: '',
      loading: false
    };
  },
   methods:{
    async checkLocationEnabled() {
      try {
        const position = await Geolocation.getCurrentPosition();
        alert("Debes tener tu ubicacion activada");
      } catch (error) {
        if (error.code === error.PERMISSION_DENIED) {
          console.error('Permiso denegado', error);
          this.promptEnableLocation();
        } else if (error.code === error.POSITION_UNAVAILABLE) {
          console.error('Posición no disponible', error);
          this.promptEnableLocation();
        } else {
          console.error('Error al obtener la ubicación', error);
        }
      }
    },

      async login() {
        this.loading = true; 
  if (this.username !== '' && this.password !== '') {
    try {
      const response = await fetch('http://localhost:3000/api/usuusuarios/consulta');
      const users = await response.json();

      const userToLogin = users.find(user => user.nom_usuario === this.username);

      if (!userToLogin) {
        console.log(response);
        throw new Error('Usuario no encontrado');
      }

      if (userToLogin.contrasena === this.password) {
        if(userToLogin.id_usu_estado === 1){
          localStorage.setItem('User-login', JSON.stringify(userToLogin)); 
          this.loading = false;
          this.username='';
          this.password='';
          alert('Inicio de sesión exitoso');
          this.$router.push("/tabs");
        }
        
        if(userToLogin.id_usu_estado === 2){
          alert('La cuenta esta Inactiva comuniquese con el Administrador');
          this.loading = false;
        }
       /* 
        if(userToLogin.idusucatestado === 3){
          alert('La cuenta esta Suspendida comuniquese con el Administrador');
          this.loading = false;
      } */
      
    }else {
        throw new Error('Contraseña incorrecta');
      }

    } catch (error) {
      this.loading = false;
      alert(('Error al iniciar sesión:', error.message));
    }
  } else {
    this.loading = false; 
    alert('Usuario y contraseña son obligatorios');
  }
},

},

created() {
    this.checkLocationEnabled();
  },
}
  
  </script>
  
  <style>
    .image-user{
      width: 120px;
      height: 120px;
    }
    .inputs{
      margin-top: 50px;
    }
    .centrado{
      display: flex;
      align-items: center;
      flex-direction: column;
    }
    .espacio-input{
      max-width: 300px;
    }
    .margen100{
      margin-top: 100px;
    }
    .item-inputs{
      margin-top: 20px;
      margin-bottom: 20px;
    }
   
    .content{
      position: relative;
      min-height: 100vh;
      padding-bottom: 80px;
    }
    .footer {
    width: 100%;
    height: 80px;
    position: absolute;
    bottom: 0;
    overflow: hidden;        /* Oculta cualquier contenido desbordado */
}

.imagefooter {
    width: 100%;
    height: 100%;
}

.full-width-image {
    width: 100%;             /* Hace que la imagen ocupe el 100% del ancho del contenedor */
    height: 80px;            /* Mantiene las proporciones de la imagen */
}
   
  </style>
<template>
    <ion-page>
        <ion-content>
            <div>
                <div class="container-set">
                    <div class="header-set">
                        <ion-img src="engranaje.png" class="header-image-set"></ion-img>
                        <ion-label class="text-set"><b>CONFIGURACIÓN</b></ion-label>
                    </div>
                </div>
                <div class="container-text-set">
                    <ion-icon :icon="personOutline" class="icon-account"></ion-icon>
                    <ion-label><b>Cuenta</b></ion-label>
                </div>
                <div class="divider-container">
                    <div class="divider"></div> 
                </div>
                <div>
                    <ion-item aria-hidden="true" :icon="createOutline" routerLink="/editprofile" lines="none" v-if="permisosAdmin">
                        <ion-icon :icon="createOutline" class="icon-style"></ion-icon>
                        <ion-label>Editar Perfil</ion-label>
                        <ion-icon slot="end" :icon="chevronForwardOutline"></ion-icon>
                    </ion-item>
                    <ion-item aria-hidden="true" routerLink="/changepassword" lines="none" v-if="permisosAdmin">
                        <ion-icon :icon="lockOpenOutline" class="icon-style"></ion-icon>
                        <ion-label>Cambiar Contraseña</ion-label>
                        <ion-icon slot="end" :icon="chevronForwardOutline"></ion-icon>
                    </ion-item>
                    <ion-item aria-hidden="true" routerLink="/deleteaccount" lines="none" v-if="permisosAdmin">
                        <ion-icon :icon="closeCircleOutline" class="icon-style"></ion-icon>
                        <ion-label>Eliminar cuenta</ion-label>
                        <ion-icon slot="end" :icon="chevronForwardOutline"></ion-icon>
                    </ion-item>
                    <ion-item aria-hidden="true" @click="logout" routerLink="/login" lines="none">
                        <ion-icon color="danger" :icon="logOutOutline" class="icon-style"></ion-icon>
                        <ion-label>Cerrar Sesion</ion-label>
                        <ion-icon slot="end" :icon="chevronForwardOutline"></ion-icon>
                    </ion-item>
                </div>
            </div>
        </ion-content>
    </ion-page>
</template>

<script>
import { ref } from 'vue';
import { IonPage, IonImg, IonLabel, IonIcon, IonItem, IonContent } from '@ionic/vue';
import { lockOpenOutline, personOutline, createOutline, closeCircleOutline, chevronForwardOutline, logOutOutline } from 'ionicons/icons';

export default {
    name: 'SettingsComponent',
    components: {
        IonPage,
        IonImg,
        IonLabel,
        IonIcon,
        IonItem,
        IonContent,

    },
    setup() {

        const TypeUserPermissions = localStorage.getItem('User-login');
        const parsedPermissions = JSON.parse(TypeUserPermissions);
        const permisosAdmin = ref(null);

        if (parsedPermissions.idusucattipousuario === 2 || parsedPermissions.idusucattipousuario === 3) {
          permisosAdmin.value = false;
        } else {
          permisosAdmin.value = true;
        }

        return {
            personOutline,
            lockOpenOutline,
            createOutline,
            closeCircleOutline,
            chevronForwardOutline,
            logOutOutline,
            permisosAdmin
        };
    },
    methods:{
        logout(){
            localStorage.removeItem('User-login'); 
        },
        userlogin(){
            const User = localStorage.getItem('User-login');
            if(User){
                console.log("estas logeado");
            }else{
                alert("No hay una sesion iniciada");
                window.location.href = '/login'; 
            }
        }
    },
    created(){
        this.userlogin();
    }
}
</script>

<style>
.container-set {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: flex-end;
  width: 100%;
  height: 140px;
  background: var(--gradient-light);
}

@media (prefers-color-scheme: dark) {
  .container-set {
    background: var(--gradient-dark);
  }
}

.header-set {
    position: absolute;
    left: 50px;
    display: flex;
    align-items: center;
    margin-bottom: 35px;
}

.header-image-set {
    width: 50px;
    height: 50px;
    margin-right: 15px;
}

.text-set {
    font-size: 21px;
}

.container-text-set {
    display: flex;
    align-items: center;
    font-size: 20px;
    margin-top: 40px;
    padding: 15px 50px;
    width: 100%;
}

.icon-account {
    margin-right: 10px; 
}

.icon-style {
    margin-right: 10px;
    color: rgb(42, 121, 195);
}

.divider-container {
    display: flex;
    justify-content: center;
    width: 100%;
}

.divider {
    height: 1px;
    width: 100%;
    margin-left: 50px;
    margin-right: 60px;
    background-color: #757575;
    margin-bottom: 20px;
}

ion-item {
    margin-left: 35px;
    margin-right: 35px;
    margin-bottom: 20px;
}

@media (prefers-color-scheme: dark) {
  ion-item {
    --background: transparent;
    --color: inherit;
  }
}
</style>

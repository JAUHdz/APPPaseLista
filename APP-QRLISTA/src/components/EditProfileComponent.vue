<template>
  <ion-page>
    <ion-content>
      <div class="container-editpro">
        <div class="header-editpro">
          <ion-img src="IEdit.png" class="header-image-editpro"></ion-img>
          <ion-label class="text-editpro"><b>EDIT PROFILE</b></ion-label>
        </div>
      </div>
      <div class="container-editprofile">
          <ion-row>
              <div class="photo-container">
                <ion-img v-if="userPhoto" :src="userPhoto" class="photo-editpro" @click="selectPhoto"></ion-img>
                <div v-else class="photo-placeholder" @click="selectPhoto"></div>
                <div class="options">
                  <ion-item lines="none">
                    <ion-icon :icon="createOutline" color="primary"></ion-icon>
                    <ion-button size="small" fill="clear" @click="selectPhoto">Editar</ion-button>
                  </ion-item>
                  <ion-item lines="none">
                    <ion-icon :icon="trashOutline" color="primary"></ion-icon>
                    <ion-button size="small" fill="clear" @click="deletePhoto">Eliminar</ion-button>
                  </ion-item>
                </div>
              </div>
        
          </ion-row>
    
        <div class="separation-data">
          <ion-label>Usuario</ion-label>
          <ion-input v-model="edusername" label-placement="floating" fill="outline" class="spacing" maxlength="10"></ion-input>
        </div>
      <div class="center">
      <ion-button class="spacebuttons" @click="handleButtonCancel" fill="outline" color="danger">Cancelar</ion-button>
      <ion-button class="spacebuttons" @click="saveUsername">Guardar</ion-button>
    </div>
  </div>
      <ion-alert
        :is-open="showAlert"
        :header="alertHeader"
        :message="alertMessage"
        :buttons="alertButtons"
        @didDismiss="showAlert = false">
      </ion-alert>
    </ion-content>
  </ion-page>
</template>

<script>
import { IonPage, IonContent, IonGrid, IonRow, IonCol, IonImg, IonButton, alertController, IonItem, IonIcon, IonAlert, IonInput, IonLabel } from '@ionic/vue';
import { Camera, CameraResultType, CameraSource } from '@capacitor/camera';
import { trashOutline, createOutline } from 'ionicons/icons';
import '@ionic/pwa-elements';
import {useRouter} from 'vue-router';

export default {
  name: 'SettingsComponent',
  components: {
    IonPage,
    IonContent,
    IonGrid,
    IonRow,
    IonCol,
    IonImg,
    IonButton,
    IonItem,
    IonIcon,
    IonAlert,
    IonInput,
    IonLabel
  },
  setup() {
    const router = useRouter();
    const handleButtonCancel = () => {
      router.push('/tabs/tab3');
    }
    return {
      trashOutline,
      createOutline,
      handleButtonCancel
    };
  },
  data() {
    return {
      defaultPhoto : 'userDefault.png',
      userPhoto: null,
      edusername: '',
      originalUsername: '',
      alertHeader: '',
      alertMessage: '',
      alertButtons: [],
      showAlert: false,
    };
  },
  async mounted() {
    this.userPhoto = this.defaultPhoto;
    this.getUsername();
  },
  methods: {
    async selectPhoto() {
      const alert = await alertController.create({
        header: 'Seleccionar imagen',
        buttons: [
          {
            text: 'Tomar foto',
            handler: () => {
              this.takePhoto();
            }
          },
          {
            text: 'Seleccionar de galería',
            handler: () => {
              this.pickPhoto();
            }
          }
        ]
      });
      await alert.present();
    },
    async takePhoto() {
      try {
        const status = await Camera.checkPermissions();
        if (status.camera === 'granted' || status.camera === 'prompt') {
          const capturedPhoto = await Camera.getPhoto({
            quality: 90,
            allowEditing: false,
            resultType: CameraResultType.DataUrl,
            source: CameraSource.Camera
          });
          this.userPhoto = capturedPhoto.dataUrl;
        } else {
          console.error('Permiso de cámara denegado');
        }
      } catch (error) {
        console.error('Error al tomar la foto:', error);
      }
    },
    async pickPhoto() {
      try {
        const galleryPhotos = await Camera.pickImages({ limit: 1 });
        if (galleryPhotos.photos.length > 0) {
          this.userPhoto = galleryPhotos.photos[0].webPath;
        }
        const alert = await alertController.getTop();
        if (alert) {
          await alert.dismiss();
        }
      } catch (error) {
        console.error('Error al seleccionar la imagen:', error);
      }
    },
    deletePhoto() {
      this.userPhoto = this.defaultPhoto;
    },
    async getUsername() {
      const userLogged = JSON.parse(localStorage.getItem('User-login'));
      if (userLogged) {
        this.edusername = userLogged.nom_usuario;
        this.originalUsername = userLogged.nom_usuario; 
      }
    },
    async saveUsername() {
      const userLogged = JSON.parse(localStorage.getItem('User-login'));
      if (!userLogged) {
        this.showAlertDialog('Error', 'No se pudo obtener el usuario logueado');
        return;
      }

      if (this.edusername === this.originalUsername) {
        this.showAlertDialog('Info', 'No hay cambios en el nombre de usuario');
        this.$router.push("/tabs/tab3");
        return;
      }

      try {
        const responseCheckUser = await fetch(`https://cemexapi20240515142245.azurewebsites.net/api/Usu_Usuarios?nom_usuario=${encodeURIComponent(this.edusername)}`);
        if (!responseCheckUser.ok) {
          this.showAlertDialog('Error', 'Error al verificar el usuario');
          return;
        }

        const existingUsers = await responseCheckUser.json();
        const userWithName = existingUsers.find(user => user.nom_usuario === this.edusername);
        if (userWithName) {
          this.showAlertDialog('Error', 'Ya existe un usuario con el mismo nombre, elige otro nombre de usuario');
          return;
        }

        const response = await fetch(`https://cemexapi20240515142245.azurewebsites.net/api/Usu_Usuarios?id=${userLogged.id_usuario}`, {
          method: 'PUT',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            id_usuario: userLogged.id_usuario,
            nom_usuario: this.edusername,
            contrasena: userLogged.contrasena,
            idusucatestado: userLogged.idusucatestado,
            idusucattipousuario: userLogged.idusucattipousuario
          })
        });
        
        if (response.ok) {
          this.showAlertDialog('Éxito', 'Nombre de usuario actualizado con éxito.');
          this.originalUsername = this.edusername;
          userLogged.nom_usuario = this.edusername;
          localStorage.setItem('User-login', JSON.stringify(userLogged));
          this.$router.push("/tabs/tab3");
        } else {
          let errorData;
          try {
            errorData = await response.json();
          } catch (e) {
            errorData = { message: response.statusText };
          }
          this.showAlertDialog('Error', `Error al actualizar el nombre de usuario: ${errorData.message}`);
        }
      } catch (error) {
        console.error("Error al cambiar el usuario:", error);
        this.showAlertDialog('Error', 'Error al cambiar el nombre de usuario.');
      }
    },
    showAlertDialog(header, message) {
      this.alertHeader = header;
      this.alertMessage = message;
      this.alertButtons = [{ text: 'Ok', handler: () => { this.showAlert = false; } }];
      this.showAlert = true;
    },
  }
};
</script>


<style>
.container-editprofile{
  align-items: center;
  flex-direction: column;
  display: flex;
}
.container-editpro {
position: relative;
display: flex;
flex-direction: column;
align-items: center;
justify-content: flex-end;
width: 100%;
height: 140px;
background: var(--gradient-light);
margin-bottom: 20px;
}

@media (prefers-color-scheme: dark) {
  .container-editpro{
    background: var(--gradient-dark);
    color: var(--ion-text-color-dark);
  }
}
.photo-editpro {
width: 100px;
height: 100px;
cursor: pointer;
object-fit: cover;
}

.photo-placeholder {
width: 100px;
height: 100px;
align-items: center;
justify-content: center;
}
@media (prefers-color-scheme: dark) {
  .text-editpro {
    color: var(--ion-text-color-dark);
  }
}



.header-editpro {
display: flex;
align-items: center;
margin-bottom: 25px;
}

.header-image-editpro {
width: 50px;
height: 50px;
margin-right: 15px;
}

.text-editpro {
font-size: 20px;
color: #000;
}
@media (prefers-color-scheme: dark) {
  .text-editpro {
    color: var(--ion-text-color-dark);
  }
}

.photo-container {
display: flex;
align-items: center;
margin-left: 50px;
}

.options {
margin-left: -30px;


}
.center {
display: flex;
justify-content: center;
}
.spacebuttons{
  margin:60px 10px
}
.container-data {
    display: flex;
    flex-direction: column;
    margin-top: 10px;
    justify-content: center;
}

.separation-data {
    margin-top: 50px;
}
@media (prefers-color-scheme: dark) {
  ion-item {
    --background: transparent;
    --color: inherit;
  }
}
</style>

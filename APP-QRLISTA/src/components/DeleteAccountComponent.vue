<template>
  <ion-page>
    <ion-content>
      <div class="container-header-delete">
        <div class="header-delete">
          <ion-img src="/delete-accunt.png" class="header-image-delete"></ion-img>
          <ion-label><b>ELIMINAR CUENTA</b></ion-label>
        </div>
      </div>
      <div class="container-delete">
        <ion-label>
          ¿Confirmas que deseas eliminar tu cuenta? 
          Se borrará de forma permanente todo tu contenido e información.
        </ion-label>
        <div class="separation-delete">
          <ion-label>Cuenta a eliminar</ion-label>
          <ion-input v-model="inputUsername" label-placement="floating" fill="outline" class="spacing-delete" maxlength="10"></ion-input>
          <ion-label>Contraseña</ion-label>
          <ion-input v-model="inputPassword" label-placement="floating" fill="outline" type="password" class="spacing-delete" maxlength="9" pattern="[a-zA-Z0-9]+">
            <ion-input-password-toggle slot="end"></ion-input-password-toggle>
          </ion-input>
          <ion-label>Indícanos por qué quieres eliminar tu cuenta (opcional)</ion-label>
          <ion-input v-model="inputComments" label-placement="floating" fill="outline" class="spacing-delete" rows="4"></ion-input>
        </div>
        <div style="display: flex;">
          <ion-button class="SpaceButton" @click="handleButtonBack" fill="outline" color="danger">Cancelar</ion-button>
          <ion-button class="SpaceButton" @click="deleteAccount" fill="solid" color="primary">Eliminar</ion-button>
        </div>
      </div>
      <ion-alert
        :is-open="showAlert"
        :header="alertHeader"
        :message="alertMessage"
        :buttons="alertButtons"
        @didDismiss="showAlert = false"
      ></ion-alert>
    </ion-content>
  </ion-page>
</template>

<script>
import { IonButton, IonContent, IonImg, IonInputPasswordToggle, IonLabel, IonPage, IonInput, IonAlert } from '@ionic/vue';
import { useRouter } from 'vue-router';

export default {
  name: 'DeleteAccountComponent',
  components: {
    IonPage,
    IonImg,
    IonInput,
    IonButton,
    IonContent,
    IonLabel,
    IonInputPasswordToggle,
    IonAlert
  },
  data() {
    return {
      inputUsername: '',
      inputPassword: '',
      inputComments: '',
      showAlert: false,
      alertHeader: '',
      alertMessage: '',
      alertButtons: []
    };
  },
  setup() {
    const router = useRouter();

    const handleButtonCancel = () => {
      router.push('/tabs/tab3');
    }
    return { 
      handleButtonCancel
    }
  },
  mounted() {
    this.clearInputsD();
  },
  methods: {
    clearInputsD() {
      this.inputUsername = '';
      this.inputPassword = '';
      this.inputComments = '';
    },
    handleButtonBack(){
      this.handleButtonCancel();
      this.clearInputsD();
    },
    async validateUser() {
      if (this.inputUsername !== '' && this.inputPassword !== '') {
        const userSaved = JSON.parse(localStorage.getItem('User-login'));

        if (userSaved && userSaved.nom_usuario === this.inputUsername && userSaved.contrasena === this.inputPassword) {
          return true;
        } else {
          this.showAlertDialog('Error', 'Datos incorrectos');
          return false;
        }
      } else {
        this.showAlertDialog('Error', 'Llena todos los campos');
        return false;
      }
    },
    async deleteAccount() {
      const validData = await this.validateUser();

      if (!validData) {
        return;
      }

      this.showConfirmDialog();
    },
    async showConfirmDialog() {
      this.alertHeader = 'Confirmación';
      this.alertMessage = '¿Estás seguro de que deseas eliminar tu cuenta?';
      this.alertButtons = [
        {
          text: 'Cancelar',
          role: 'cancel',
          handler: () => {
            this.showAlert = false;
          }
        },
        {
          text: 'Eliminar',
          handler: () => {
            this.processDeleteAccount();
          }
        }
      ]; this.showAlert = true;
    },
    async processDeleteAccount() {
      const userSaved = JSON.parse(localStorage.getItem('User-login'));

      try {
        const response = await fetch(`https://cemexapi20240515142245.azurewebsites.net/api/Usu_Usuarios?id=${userSaved.id_usuario}`, {
          method: 'DELETE'
        });

        if (response.ok) {
          this.showAlertDialog('Excelente!', 'Cuenta eliminada correctamente');
          this.clearInputsD();
          localStorage.removeItem('User-login');
          this.$router.push("/login");
        } else {
          this.showAlertDialog('Error','No se pudo eliminar la cuenta');
        }
      } catch (error) {
        this.showAlertDialog('Error', 'Existe informacion aliada a esta cuenta');
      }
    },
    showAlertDialog(header, message) {
      this.alertHeader = header;
      this.alertMessage = message;
      this.alertButtons = [
        {
          text: 'Ok',
          handler: () => {
            this.showAlert = false;
          }
        }
      ];
      this.showAlert = true;
    }
  }
};
</script>

<style>
.container-text-delete {
    justify-content: center;
    align-items: center;
    margin-top: 30px;
    margin-left: 60px;
    margin-right: 60px;
    display: flex;
    flex-direction: column;
}
.container-delete {
    justify-content: center;
    align-items: center;
    margin-left: 60px;
    margin-right: 60px;
    display: flex;
    flex-direction: column;
    margin-top: 50px;
}
.container-header-delete {
    position: relative;
    display: flex;
    align-items: center;
    flex-direction: column;
    justify-content: flex-end;
    width: 100%;
    height: 100px;
}
.header-delete {
    display: flex;
    align-items: center;
    margin-top: 25px;
    font-size: 1.5rem;
}
.header-image-delete {
    width: 50px;
    height: 50px;
    margin-right: 15px;
}
.separation-delete {
    margin-top: 50px;
}
.spacing-delete {
    margin-bottom: 25px;
    margin-top: 20px;
}
.spacing ion-label {
    margin-bottom: 10px;
}

.SpaceButton {
  margin: 40px 15px ;
}
</style>

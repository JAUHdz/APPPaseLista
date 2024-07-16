<template>
    <ion-page>
        <ion-content>
            <div class="container-header-cp">
                <div class="header-cp">
                    <ion-icon class="header-icon-cp" :icon="arrowBackOutline" @click.prevent="handleIconBack"></ion-icon>
                    <ion-label class="change"><b>CAMBIAR CONTRASEÑA</b></ion-label>
                </div>
            </div>
            <div class="container-cp">
                <div class="separation-cp">
                    <ion-label>Contraseña actual</ion-label>
                    <ion-input v-model="currentPassword" label-placement="floating" fill="outline" type="password" class="spacing-cp" maxlength="10" pattern="[a-zA-Z0-9]+">
                        <ion-input-password-toggle slot="end"></ion-input-password-toggle>
                    </ion-input>
                    <ion-label>Contraseña nueva</ion-label>
                    <ion-input v-model="newPassword" @ionInput="validatePasswords" label-placement="floating" fill="outline" type="password" class="spacing-cp" maxlength="10" pattern="[a-zA-Z0-9]+">
                        <ion-input-password-toggle slot="end"></ion-input-password-toggle>
                    </ion-input>
                    <ion-label>Confirmar contraseña nueva</ion-label>
                    <ion-input v-model="confirmNewPassword" @ionInput="validatePasswords" label-placement="floating" fill="outline" type="password" class="spacing-cp" maxlength="10" pattern="[a-zA-Z0-9]+">
                        <ion-input-password-toggle slot="end"></ion-input-password-toggle>
                    </ion-input>
                    <span v-if="errors.confirmNewPassword" class="error-message">{{ errors.confirmNewPassword }}</span>
                </div>
                <div class="Button-container">
                    <ion-button @click.prevent="changePassword" fill="solid" color="primary" class="change-password-button">Cambiar</ion-button>
                </div>
            </div>
            <ion-alert
                :is-open="showAlert"
                :header="alertHeader"
                :message="alertMessage"
                :buttons="alertButtons"
                @didDismiss="showAlert=false">
            </ion-alert>
        </ion-content>
    </ion-page>
</template>

<script>
import { IonPage, IonInput, IonButton, IonContent, IonLabel, IonInputPasswordToggle, IonIcon, IonAlert } from '@ionic/vue';
import { useRouter } from 'vue-router';
import { arrowBackOutline } from 'ionicons/icons';

export default {
    name: 'ChangePasswordComponent',
    components: {
        IonPage,
        IonInput,
        IonButton,
        IonContent,
        IonLabel,
        IonInputPasswordToggle,
        IonIcon,
        IonAlert
    },
    data() {
        return {
            currentPassword: '',
            newPassword: '',
            confirmNewPassword: '',
            alertHeader: '',
            alertMessage: '',
            alertButtons: [],
            showAlert: false,
            errors: {
                newPassword: '',
                confirmNewPassword: ''
            }
        };
    },
    setup() {
        const router = useRouter();
        const redirectSettingsDashboard = () => {
            router.push('/tabs/tab3');
        };
        return {
            redirectSettingsDashboard,
            arrowBackOutline
        };
    },
    mounted() {
        this.clearInputsC();
    },
    methods: {
        clearInputsC() {
            this.currentPassword = '';
            this.newPassword = '';
            this.confirmNewPassword = '';
            this.errors.newPassword = '';
            this.errors.confirmNewPassword = '';
        },
        async changePassword() {
            if (this.currentPassword !== '' && this.newPassword !== '' && this.confirmNewPassword !== '') {
                if (this.newPassword !== this.confirmNewPassword) {
                    this.errors.confirmNewPassword = 'Las contraseñas no coinciden';
                    return;
                }
                const userLoggedIn = JSON.parse(localStorage.getItem('User-login'));
                if (userLoggedIn && userLoggedIn.contrasena === this.currentPassword) {
                    await this.processChangePassword(userLoggedIn);
                } else {
                    this.showAlertDialog('Error', 'La contraseña actual es incorrecta.');
                }
            } else {
                this.showAlertDialog('Error', 'Por favor, completa todos los campos.');
            }
        },
        async processChangePassword(userLoggedIn) {
            try {
                const response = await fetch('https://cemexapi20240515142245.azurewebsites.net/api/Usu_Usuarios', {
                    method: 'PUT',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        id_usuario: userLoggedIn.id_usuario,
                        nom_usuario: userLoggedIn.nom_usuario,
                        contrasena: this.newPassword,
                        idusucatestado: userLoggedIn.idusucatestado,
                        idusucattipousuario: userLoggedIn.idusucattipousuario
                    })
                });

                if (response.ok) {
                    userLoggedIn.contrasena = this.newPassword;
                    localStorage.setItem('User-login',JSON.stringify(userLoggedIn));
                    this.showAlertDialog('Éxito', 'Contraseña actualizada con éxito.');
                    this.clearInputsC();
                    this.redirectSettingsDashboard();
                } else {
                    let errorData;
                    try {
                        errorData = await response.json();
                    } catch (e) {
                        errorData = { message: response.statusText };
                    }
                    this.showAlertDialog('Error', 'Error al actualizar la contraseña');
                }
            } catch (error) {
                console.error("Error al cambiar la contraseña:", error);
                this.showAlertDialog('Error', 'Error al cambiar la contraseña.');
            }
        },
        handleIconBack() {
            this.redirectSettingsDashboard();
            this.clearInputsC();
        },
        showAlertDialog(header, message) {
            this.alertHeader = header;
            this.alertMessage = message;
            this.alertButtons = [{ text: 'Ok', handler: () => { this.showAlert = false; } }];
            this.showAlert = true;
        },
        validatePasswords() {
            if (this.newPassword === '') {
                this.errors.newPassword = 'Ingresa tu nueva contraseña';
            } else {
                this.errors.newPassword = '';
            }
            if (this.confirmNewPassword === '') {
                this.errors.confirmNewPassword = 'Confirma tu nueva contraseña';
            } else if (this.confirmNewPassword !== this.newPassword) {
                this.errors.confirmNewPassword = 'Las contraseñas no coinciden';
            } else {
                this.errors.confirmNewPassword = '';
            }
        }
    }
};

</script>

<style>
.container-header-cp {
    display: flex;
    align-items: center;
    flex-direction: column;
    justify-content: end;
    width: 100%;
    height: 120px;
}

.header-cp {
    display: flex;
    align-items: end;
    margin-bottom: 30px;
}

.header-icon-cp {
    width: 25px;
    height: 25px;
    margin-right: 20px;
}

.change {
    margin-top: 25px;
    font-size: 21px;
}

.container-cp {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-top: 20px;
}

.separation-cp {
    margin-top: 50px;
}
.spacing-cp {
    margin-bottom: 25px;
    margin-top: 20px;
}

.error-message {
    color: red;
    font-size: 14px;
    margin-top: 5px;
    margin-bottom: 10px;
}

.Button-container {
    display: flex;
    justify-content: center;
}

.change-password-button {
    margin-top: 30px;
    position: absolute;
}

</style>

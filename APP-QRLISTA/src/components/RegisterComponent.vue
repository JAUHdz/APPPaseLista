<template>
    <ion-page>
        <ion-content>
            <ion-img src="/HdLogin.png"></ion-img>
            <div class="container">
                <ion-label class="register"><b>REGISTRO</b></ion-label>
                <div class="separation">
                    <ion-label>Nombre</ion-label>
                    <ion-input v-model="nombre" label-placement="floating" fill="outline" class="spacing" maxlength="10"></ion-input>
                    <ion-label>Apellido Paterno</ion-label>
                    <ion-input v-model="apellidop" label-placement="floating" fill="outline" class="spacing" maxlength="10"></ion-input>
                    <ion-label>Apellido Materno</ion-label>
                    <ion-input v-model="apellidom" label-placement="floating" fill="outline" class="spacing" maxlength="10"></ion-input>
                    <ion-label>Nombre de Usuario</ion-label>
                    <ion-input v-model="username" label-placement="floating" fill="outline" class="spacing" maxlength="10"></ion-input>
                    <ion-label>Contraseña</ion-label>
                    <ion-input v-model="password" label-placement="floating" fill="outline" type="password" class="spacing" maxlength="9" pattern="[a-zA-Z0-9]+">
                        <ion-input-password-toggle slot="end"></ion-input-password-toggle>
                    </ion-input>
                    <ion-label>Estado</ion-label>
                    <ion-item class="spacing">
                        <ion-select v-model="selectedState" placeholder="Activo" class="ion-select-fixed">
                            <ion-select-option value="1">Activo</ion-select-option>
                            <ion-select-option value="2">Inactivo</ion-select-option>
                        </ion-select>
                    </ion-item>
                    <ion-label>Tipo</ion-label>
                    <ion-item class="spacing">
                        <ion-select aria-label="Tipo Usuario" v-model="selectedType" placeholder="Administrador" class="ion-select-fixed">
                            <ion-select-option value="1">Administrador</ion-select-option>
                            <ion-select-option value="2">Empleado</ion-select-option>
                        </ion-select>
                    </ion-item>
                </div>
                <div class="button-container">
                    <ion-button @click="register" fill="solid" class="blue-button" style="margin-right: 20px;">Registrar</ion-button>
                    <ion-button color="danger" @click="salir">Cancelar</ion-button>
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
import { IonImg, IonPage, IonItem, IonInput, IonSelect, IonSelectOption, IonButton, IonContent, IonLabel, IonInputPasswordToggle, IonAlert } from '@ionic/vue';

export default {
    name: 'RegisterComponent',
    components: {
        IonPage,
        IonImg,
        IonItem,
        IonInput,
        IonSelect,
        IonSelectOption,
        IonButton,
        IonContent,
        IonLabel,
        IonInputPasswordToggle,
        IonAlert
    },
    data() {
        return {
            username: '',
            nombre:'',
            apellidop:'',
            apellidom:'',
            password: '',
            selectedState: '',
            selectedType: '',
            showAlert: false,
            alertHeader: '',
            alertMessage: '',
            alertButtons: []
        };
    },
    methods: {
        async register() {
            try {
                if (!this.username || !this.password || !this.selectedState || !this.selectedType) {
                    this.showAlertDR('Error', 'Todos los campos son obligatorios');
                    return;
                }

                const responseCheckUser = await fetch(`http://localhost:3000/api/usuusuarios/consulta`);
                if (!responseCheckUser.ok) {
                    this.showAlertDR('Error al verificar el usuario');
                    return;
                }
                
                const existingUsers = await responseCheckUser.json();
                const userWithName = existingUsers.find(user => user.nom_usuario === this.username);
                if (userWithName) {
                    this.showAlertDR('Error','Ya existe un usuario con el mismo nombre, elige otro nombre de usuario');
                    return;
                }

                const registerReply = await fetch('http://localhost:3000/api/usuusuarios/crear', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        nombre: this.nombre,
                        apellido_paterno: this.apellidop,
                        apellido_materno: this.apellidom,
                        nom_usuario: this.username,
                        contrasena: this.password,
                        id_usu_tipo: parseInt(this.selectedType),
                        id_usu_estado: parseInt(this.selectedState)
                    })
                });

                if (!registerReply.ok) {
                    this.showAlertDR('Error', 'Error en el registro, por favor intenta nuevamente');
                    return;
                }

                this.showAlertDR('Excelente!', 'Registro exitoso');
                this.$router.push("/tabs");
            } catch (error) {
                console.error('Error en el registro:');
                this.showAlertDR('Error', 'Por favor intenta nuevamente');
            }
        },
        showAlertDR(header, message) {
            this.alertHeader = header;
            this.alertMessage = message;
            this.alertButtons = [{
                text: 'Ok',
                handler: () => {
                    this.showAlert = false;
                }
            }];
            this.showAlert = true;
        },
        salir(){
            this.$router.push("/tabs");
        }
    }
};
</script>

<style>
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
}
.separation {
    margin-top: 50px;
}
.image {
    width: 100%;
}
.register {
    margin-top: 25px;
    font-size: 1.5em;
    text-align: left;
}
.spacing{
    margin-bottom: 25px;
    margin-top: 20px;
}
.spacing ion-label {
    margin-bottom: 10px;
}
.ion-item {
    display: flex;
    align-items: center;
}

.spacing ion-select::part(icon) {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}

.button-container {
    display: flex;
    flex-direction: row;
    margin-top: 40px !important;
    justify-content: center;
    margin-bottom: 70px;
    width: 100%;
}
</style>
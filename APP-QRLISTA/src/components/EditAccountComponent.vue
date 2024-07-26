<template>
    <ion-page>
        <ion-content>
        <div class="container-header">
            <div class="header">
            <ion-img src="/Edit.png" class="header-image"></ion-img>
            <ion-label class="edit"><b>EDITAR USUARIO</b></ion-label>
        </div>
        </div>
        <div class="container">
            <div class="separation">
                <ion-label>Nombre</ion-label>
                <ion-input v-model="nombre" label-placement="floating" fill="outline" class="spacing" maxlength="30" :value="username"></ion-input>

                <ion-label>Apellido Paterno</ion-label>
                <ion-input v-model="apellp" label-placement="floating" fill="outline" class="spacing" maxlength="20" :value="username"></ion-input>

                <ion-label>Apellido Materno</ion-label>
                <ion-input v-model="apellm" label-placement="floating" fill="outline" class="spacing" maxlength="20" :value="username"></ion-input>

                <ion-label>Usuario</ion-label>
                <ion-input v-model="username" label-placement="floating" fill="outline" class="spacing" maxlength="10" :value="username"></ion-input>
                
                <ion-label>Contraseña</ion-label>
                <ion-input v-model="password" label-placement="floating" fill="outline" type="password" class="spacing" maxlength="9" pattern="[a-zA-Z0-9]+">
                <ion-input-password-toggle slot="end"></ion-input-password-toggle></ion-input>
                
                <ion-label>Estado</ion-label>
                <ion-item class="spacing">
                    <ion-select v-model="selectedState" class="ion-select-fixed" :value="selectedState">
                        <ion-select-option v-for="state in StateData" :value="state.id_estado" :key="state.id_estado">{{ state.nom_estado }}</ion-select-option>
                    </ion-select>
                </ion-item>
                
                <ion-label>Tipo</ion-label>
                <ion-item class="spacing">
                    <ion-select aria-label="Tipo Usuario" v-model="selectedType" placeholder="Seleccione un tipo" class="ion-select-fixed">
                        <ion-select-option v-for="tipo in TypeData" :value="tipo.id_tipo" :key="tipo.id_tipo">{{ tipo.nom_tipo }}</ion-select-option>
                    </ion-select>
                </ion-item>
            </div>
            
            <div class="Button-container">
                <ion-button class="button-space" @click.prevent="handleButtonCancel" fill="outline"  color="danger">CANCELAR</ion-button>
                <ion-button class="button-space blue-button" @click.prevent="UpdateUser(IdUserEdit)" fill="solid">GUARDAR</ion-button>
            </div>
        </div>
    </ion-content>
    </ion-page>
</template>

<script>
import { IonImg, IonPage, IonItem, IonInput, IonSelect, IonSelectOption, IonButton, IonContent, IonLabel , IonInputPasswordToggle } from '@ionic/vue';
import { useRouter } from 'vue-router';

export default {
    name: 'EditAccountComponent',
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
        
    },
    data() {
        return {
            nombre:'',
            apellp:'',
            apellm: '',
            username: '',
            password: '',
            selectedState: '',
            selectedType: '',
            IdUserEdit: null,
            DataUser: [],
            StateData:[],
            TypeData: []
        };
    },
    setup() {
        const router = useRouter();

        const redirectBoardUsers = () => {
            router.push('/board');
        }
        
        return {
            redirectBoardUsers
        };
    },
    created() {
        this.UserData();
    },
    methods: {
         async UserData () {
            try {
                const id_user_edit = localStorage.getItem('id-user-edit');
                this.IdUserEdit = JSON.parse(id_user_edit);
                console.log(this.IdUserEdit);

                const responseUsuarios = await fetch(`https://apicontrolhorario.onrender.com/api/usuusuarios/consulta/${this.IdUserEdit}`);
                this.DataUser = await responseUsuarios.json();
                console.log("Consulta exitosa");
            } catch (error) {
                console.error("Error en la consulta de Usuarios:", error);
            }

            try {
                const responseTipo = await fetch('https://apicontrolhorario.onrender.com/api/tipousuarios/consulta');
                this.TypeData = await responseTipo.json();
                console.log("Consulta exitosa");
            } catch (error) {
                console.error("Error en la consulta de Tipos de usuarios:", error);
            }          
            
            try {
                const responseEstado = await fetch('https://apicontrolhorario.onrender.com/api/estadousuarios/consulta');
                this.StateData = await responseEstado.json();
                console.log("Consulta exitosa");
            } catch (error) {
                console.error("Error en la consulta de Estados de usuarios:", error);
            }

            if (this.DataUser) {
                this.nombre = this.DataUser.nombre;
                this.apellp = this.DataUser.apellido_paterno;
                this.apellm = this.DataUser.apellido_materno;
                this.username = this.DataUser.nom_usuario;
                this.password = this.DataUser.contrasena;
                this.selectedState = this.DataUser.id_usu_estado;
                this.selectedType = this.DataUser.id_usu_tipo;
            }
        },

        async UpdateUser(id){

            try {
                const response = await fetch(`https://apicontrolhorario.onrender.com/api/usuusuarios/actualizar/${id}`, {
                    method: 'PUT',
                    headers: {'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        nombre: this.nombre,
                        apellido_paterno: this.apellp,
                        apellido_materno: this.apellm,
                        nom_usuario: this.username,
                        contrasena: this.password,
                        id_usu_tipo: this.selectedType,
                        id_usu_estado: this.selectedState
                    })
                });

                if (response.ok) {
                    alert ('Usuario actualizado');
                    this.redirectBoardUsers();
                } else {
                    console.log('El nombre de Usuario ya existe');
                    console.error('Error al actualizar usuario');
                }
            } catch (error) {
                alert((error));
            }
        },

        async handleButtonCancel(){
            localStorage.removeItem('id-user-edit');
            this.redirectBoardUsers();
        }
    },
    watch: {
        '$route'(to) {
            if (to.path === '/editaccount') {
                this.UserData();
            }
        }
    }


}
</script>


<style>
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
}
.container-header {
    position: relative;
    display: flex;
    align-items: center;
    flex-direction: column;
    justify-content: flex-end;
    width: 100%;
    height: 130px;
}
.header {
    display: flex;
    align-items: center;
    margin-bottom: 30px;
}

.header-image {
    width: 50px;
    height: 50px;
    margin-right: 15px;
}
.separation{
    margin-top: 50px;
}
.edit {
    margin-top: 25px;
    font-size: 1.5em;
}
.spacing{
    margin-bottom: 25px;
    margin-top: 20px;
}
.spacing ion-label {
    margin-bottom: 10px;
}

.spacing ion-select::part(icon) {
    position: absolute;
    right: 0px;
    top: 50%;
    transform: translateY(-50%);
}


.Button-container {
    display: flex;
}

.button-space {
    margin: 50px 15px;
}
</style>

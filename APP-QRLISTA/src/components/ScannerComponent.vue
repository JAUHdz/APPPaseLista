<template>
  <ion-page>
    <ion-header>
      <toolbar-reutilizable-component :title="'Control Horario'"/>
    </ion-header>
    <ion-content>
      
      <ion-card>
        <ion-card-header>
          <p class="text-center">Para escánear los códigos QR has click en el boton flotante del scanner que se encuentra en la parte inferior derecha</p>
        </ion-card-header>
      </ion-card>
      
      <ion-card>
        <ion-card-header>
          <ion-card-title class="text-center">Registros Entrada/Salida</ion-card-title>
        </ion-card-header>
        
        <ion-card-content style="height: 320px;">
          <ion-grid>

            <ion-row class="custom-border-bottom">
              <ion-col class="Center-icons">
                <ion-icon style="width: 90px; height: 90px;" color="primary" :icon="scanOutline"></ion-icon>
              </ion-col>
            </ion-row>

            <div style="height: 220px; overflow-y: auto;">
              <p class="text-center">Escáne el código para registrar su entrada o salida</p>
            </div>

            
          </ion-grid>
        </ion-card-content>
        
      </ion-card>

      <ion-card>
        <ion-card-header>
          <ion-card-title>Informacion sobre los datos </ion-card-title>
        </ion-card-header>
        <ion-card-content>
          <p style="text-align: justify">Todos los datos recopilados e utilizados en la aplicación están seguros y solo pueden acceder a ellos personal administrativo</p>
        </ion-card-content>
      </ion-card>
    </ion-content>
    
    <ion-fab horizontal="end" vertical="bottom">
      <ion-fab-button @click="openCameraModal()" class="Center-icons">
        <ion-icon :icon="scanOutline"></ion-icon>
      </ion-fab-button>
    </ion-fab>
    <ion-modal :is-open="cameraModalOpen" @did-dismiss="closeCameraModal">
      <ion-page class="fondo">
        <div>
            <h1 class="titulo">Scan your code QR</h1>
            <div class="centrado">
                <div class="contenedor borde">
                 <qrcode-stream @detect="onDetect"></qrcode-stream>
              </div>
              <ion-button style="margin-top:20px;" color="danger" @click="cancelar">Cancelar</ion-button>
            </div>
        </div>
    </ion-page>
    </ion-modal>
  </ion-page>
</template>

<script setup>
import { ref} from 'vue';
import { IonPage, IonContent, IonButton, IonCard, IonCardContent, IonCardHeader, IonCardTitle, IonFab, IonFabButton, IonHeader, IonLabel, IonModal,IonIcon, IonGrid, IonRow, IonCol} from '@ionic/vue';
import { QrcodeStream } from 'vue-qrcode-reader';
import {scanOutline, archiveOutline, busOutline} from 'ionicons/icons';
import ToolbarReutilizableComponent from '../components/ToolbarReutilizableComponent.vue'

const qrCodes = ref(null);
const cameraModalOpen = ref(false);
const ubicacion = ref(null);
const ubicacionDetallada = ref('');
let x=0;

const Userlogin = localStorage.getItem('User-login');
const Userid = JSON.parse(Userlogin);

const onDetect = (detectedCodes) => {
  detectedCodes.forEach(code => {
    qrCodes.value=code.rawValue;
    console.log(qrCodes.value);
  });
  localStorage.setItem('qrCodes', JSON.stringify(qrCodes.value));
  closeCameraModal();
  controlHorario();
};

const openCameraModal = () => {
  cameraModalOpen.value = true;
  obtenerUbicacion();
};

const closeCameraModal = () => {
  cameraModalOpen.value = false;
};

// Método para obtener la ubicación actual
const obtenerUbicacion = async () => {
  try {
    const resp = await navigator.geolocation.getCurrentPosition(
      (position) => {
        ubicacion.value = position;
        obtenerUbicacionDetallada(position.coords.latitude, position.coords.longitude);
      },
      (error) => {
        console.log('Error al obtener la ubicación', error, resp);
      }
    );
  } catch (error) {
   alert('Error al obtener la ubicación', error);
  }
};

// Método para obtener la ubicación detallada
const obtenerUbicacionDetallada = async (lat, lon) => {
  try {
    const response = await fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lon}`);
    const data = await response.json();
    ubicacionDetallada.value = data.display_name;
  } catch (error) {
   alert('Error al obtener la ubicación detallada', error);
    ubicacionDetallada.value = 'Ubicación desconocida';
  }
};

const getCurrentDate = () => {
      const date = new Date();
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(2, '0');
      const day = String(date.getDate()).padStart(2, '0');
      return `${year}-${month}-${day}`;
    }

   const getCurrentTime = ()=> {
      const date = new Date();
      const hours = String(date.getHours()).padStart(2, '0');
      const minutes = String(date.getMinutes()).padStart(2, '0');
      const seconds = String(date.getSeconds()).padStart(2, '0');
      return `${hours}:${minutes}:${seconds}`;
    }

//Metodo para registrar en la entrada y salida 

const controlHorario = () => {
  for (let z=0; z<2; z++){

    const cid = localStorage.getItem('idControlHorario');
    const conid = cid ? JSON.parse(cid) : null;

      if (conid) {
      x=1;
    } else {
      x = 0;
    }

    if(x==1){
      if(qrCodes.value=="SALIDA"){
        console.log("SALIDA");
        postsalida(); 
        x=0;
        z=2;
      }else{
        alert("No se escaneo el código QR Adecuado para la salida, escanee el correcto por favor");
        z=2;
      }
    }

    if(x==0 && z<2){
      if(qrCodes.value=="ENTRADA"){
        x=x+1;
        console.log("ENTRADA");
        postentrada();
        z=2;
      }else{
        alert("No se escaneo el código QR Adecuado para la entrada, escanee el correcto por favor");
        z=2;
      }
    }
  }
}

const postentrada = async () => {
  const fecha = getCurrentDate();
  const horaEntrada = getCurrentTime();
  const horaSalida = null; 
  
  const data = {
    id_usuario: Userid.id_usuario,
    fecha: fecha,
    hora_entrada: horaEntrada,
    hora_salida: horaSalida,
    localizacion: ubicacionDetallada.value 
  };

  try {
    const response = await fetch('https://apicontrolhorario.onrender.com/api/controlhorario/crear', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(data)
    });

    if (!response.ok) {
      throw new Error('Error en la solicitud');
    }

    const result = await response.json();
    console.log('Respuesta del servidor:', result);

    localStorage.setItem('idControlHorario', JSON.stringify(result));
    localStorage.setItem('ControlHorario', JSON.stringify(data));
    alert("Se ha registrado su entrada correctamente");

  } catch (error) {
    console.error('Error en la solicitud:', error);
    alert('Error en la solicitud:', error);
  }
}

const postsalida = async ()=>{
  const cid = localStorage.getItem('idControlHorario');
  const conid = JSON.parse(cid);
  console.log("id",conid.id)

  const c = localStorage.getItem('ControlHorario');
  const con = JSON.parse(c);
  console.log("datos",con);

  const Salida = getCurrentTime();
  const datos = {
    id_usuario: con.id_usuario, 
    fecha: con.fecha,
    hora_entrada: con.hora_entrada,
    hora_salida: Salida,
    localizacion: con.localizacion
  };
  try {
    const response = await fetch(`https://apicontrolhorario.onrender.com/api/controlhorario/actualizar/${conid.id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(datos)
    });

    if (!response.ok) {
      throw new Error('Error en la solicitud');
    }

    const result = await response.json();
    console.log('Respuesta del servidor:', result);

    localStorage.removeItem('idControlHorario');
    localStorage.removeItem('ControlHorario');
    alert("Se ha registrado su hora de salida correctamente");

  } catch (error) {
    console.error('Error en la solicitud:', error);
    alert('Error en la solicitud:', error);
  }
};
</script>

<style>
.custom-dialog {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.dialog-content {
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
}
@media (prefers-color-scheme: dark) {
  .dialog-content {
    background-color: black;
  }
}
.container-camara {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  height: 100%;
  width: 100%;
  max-width: 400px;
  max-height: 400px;
  margin: 0 auto;
  padding-top: 20px;
}

.highlight {
  text-align: center;
  color: black;
}
.highlight-green{
  background-color: #A9D1F6;
  text-align: center;
  color: black;
}

.borde{
  margin: 3px;
  border-width: 6px;
  border-style: solid;
  border-color: rgb(161, 163, 189);
}
.fondo {
  background: linear-gradient(0deg, #D1E1E6, #FFFFFF);
}
.contenedor{
    width: 250px;
    height: 250px;
    margin-top: 150px;
}
.centrado{
    display: flex;
    justify-content: center;
}
.titulo{
    margin-top: 100px;
    text-align: center;
}

.text-center {
  text-align: center;
  
}
.Center-icons {
  display: flex;
  align-items: center;
  justify-content: center;
}

.size-icons {
  width: 30px;
  height: 30px;
}

.size-icons-after-camera {
  width: 40px;
  height: 40px;
}

.custom-border-bottom {
  border-bottom: 1px solid #b3a6a6;
  padding-bottom: 15px;
  margin-bottom: 15px;
}
.custom-border-top {
  border-top: 1px solid #b3a6a6;
}

</style>

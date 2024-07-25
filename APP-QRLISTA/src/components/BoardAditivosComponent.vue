<template>
    <ion-page>
      <ion-content>
        <ion-refresher slot="fixed" @ionRefresh="handleRefresh">
          <ion-refresher-content></ion-refresher-content>
        </ion-refresher>
        <ion-card>
          <ion-grid>
            <ion-row>
              <ion-col class="pagination-text" size="12">
                <h3>Tabla Control Horario</h3>
              </ion-col>
            </ion-row>
            <ion-row style="margin-bottom: 15px">
              <ion-col>
                <ion-input placeholder="Nombre" v-model="searchName"></ion-input>
              </ion-col>
              <ion-col>
                <ion-input type="time" laceholder="Vaciado" v-model="searchVaciado"></ion-input>
              </ion-col>
              <ion-col>
                <ion-input  type="date" placeholder="Fecha" v-model="searchDate"></ion-input>
              </ion-col>
            </ion-row>
            <ion-row>
                <ion-col class="pagination-text">Usuario</ion-col>
                <ion-col class="pagination-text">Fecha</ion-col>
                <ion-col class="pagination-text">Hora de entrada</ion-col>
                <ion-col class="pagination-text">Hora de salida</ion-col>
                <ion-col class="pagination-text">Ubicacion</ion-col>
                <ion-col class="pagination-text">Acciones</ion-col>
            </ion-row>
            <!-- Verificar si usuarioTabla está vacío o no -->
            <ion-row v-if="paginatedControl.length === 0">
              <ion-col>No hay registros para mostrar</ion-col>
            </ion-row>

            <ion-row v-else v-for="control in paginatedControl" :key="control.id_aditivos" class="table-space">
                <ion-col class="pagination-text">{{ control.UsuarioDato ? control.UsuarioDato.nombre : 'N/A' }}</ion-col>
                <ion-col class="pagination-text">{{ control.fecha }}</ion-col>
                <ion-col class="pagination-text">{{ control.hora_entrada }}</ion-col>
                <ion-col class="pagination-text">{{ control.hora_salida }}</ion-col>
                <ion-col class="pagination-text">{{ control.localizacion }}</ion-col>
                <ion-col class="pagination-text">
                  <ion-icon :icon="trashOutline" class="Icon" color="danger" @click.prevent="openalert(control.id_control)"></ion-icon>
                  <ion-alert :is-open="deletealert" class="custom-alert" header="Estás seguro de eliminar este registro?" :buttons="alertButtons"></ion-alert>
                </ion-col>
            </ion-row>
            <ion-row class="pagination-row">
              <ion-col class="pagination-icon">
                <ion-icon class="icon" color="primary" :icon="chevronBackOutline" @click.prevent="prevPage" :disabled="currentPage === 1"></ion-icon>
              </ion-col>
              <ion-col size="auto" class="pagination-text">
                <span>Página {{ currentPage }} de {{ totalPages }}</span>
              </ion-col>
              <ion-col class="pagination-icon">
                <ion-icon class="icon" color="primary" :icon="chevronForwardOutline" @click.prevent="nextPage" :disabled="currentPage === totalPages"></ion-icon>
              </ion-col>
            </ion-row>
          </ion-grid>
        </ion-card>
      </ion-content>
    </ion-page>
  </template>
  
  <script>
  import { IonPage, IonContent, IonCard, IonGrid, IonCol, IonRow, IonIcon, IonInput } from '@ionic/vue';
  import { ScreenOrientation } from '@capacitor/screen-orientation';
  import { createOutline, trashOutline, chevronBackOutline, chevronForwardOutline, arrowBackOutline} from 'ionicons/icons';
  
  export default {
    name: 'BoardAditivosComponent',
    components: {
      IonPage,
      IonContent,
      IonCard,
      IonGrid, 
      IonCol, 
      IonRow,
      IonIcon,
      IonInput,
    },
    data (){
        return {
            controlTabla: [],
            usuarioTabla: [],
            searchName: '',
            searchDate: '',
            searchVaciado:'',
            currentPage: 1,
            itemsPerPage: 5,
            deletealert: false,
            id:'',
        }
    },
    setup() {
      return {
        createOutline,
        trashOutline,
        chevronBackOutline,
        chevronForwardOutline,
        arrowBackOutline
      }
    },
    methods: {
      async ConsultarRegistros() {
            try {
                const responseAditivos = await fetch('http://localhost:3000/api/controlhorario/consulta');
                this.controlTabla = await responseAditivos.json();
                console.log("Consulta exitosa de aditivos");
            } catch (error) {
                console.error("Error en la consulta de los Aditivos:", error);
            }
  
            try {
                const responseUsuarios = await fetch('http://localhost:3000/api/usuusuarios/consulta');
                this.usuarioTabla = await responseUsuarios.json();
                console.log("Consulta exitosa de usuarios");
            } catch (error) {
                console.error("Error en la consulta de Usuarios:", error);
            }
  
            this.controlTabla = this.controlTabla.map(control => {
              control.UsuarioDato = this.usuarioTabla.find(usuario => usuario.id_usuario === control.id_usuario);  
                return control;
            });
      },

      nextPage() {
        if (this.currentPage < this.totalPages) {
          this.currentPage++;
        }
      },
      
      prevPage() {
        if (this.currentPage > 1) {
          this.currentPage--;
        }
      },

      async handleRefresh(event) {
        await this.ConsultarRegistros();
        event.target.complete();  
      },
      openalert(id_user){
        this.deletealert = true;
        this.id = id_user;
      },
      resetDeleteAlert() {
        this.deletealert= false;
      },

      async DeleteGetId(id_c) {
      try {
        await fetch(`http://localhost:3000/api/controlhorario/eliminar/${id_c}`, {
        method: 'DELETE'
        });
        alert('Registro eliminado correctamente')
        this.deletealert= false;
        this.ConsultarRegistros();
      } catch (error) {
        alert("Error al eliminar el Registro:");
        this.deletealert= false;
      }
    },
    },
    computed: {
      
      filteredControl() {
        return this.controlTabla.filter(control => {
          const matchesName = this.searchName ? (control.UsuarioDato && control.UsuarioDato.nombre.toLowerCase().includes(this.searchName.toLowerCase())) : true;
          const matchesDate = this.searchDate ? control.fecha.substring(0, 10) === this.searchDate.substring(0, 10) : true;
          const controlHora = control.hora_entrada ? control.hora_entrada.substring(0, 5) : '';
          const searchHora = this.searchVaciado ? this.searchVaciado.substring(0, 5) : '';
          const matchesVaciado = this.searchVaciado ? controlHora === searchHora : true;
          return matchesName && matchesDate && matchesVaciado;
        });
      },

      paginatedControl() {
        const start = (this.currentPage - 1) * this.itemsPerPage;
        const end = start + this.itemsPerPage;
        return this.filteredControl.slice(start, end);
      },
    
      totalPages() {
        return Math.ceil(this.filteredControl.length / this.itemsPerPage);
      },
      alertButtons() {
      return [
        {
          text: 'No',
          handler: this.resetDeleteAlert
        },
        {
          text: 'Si',
          handler: async () => {
            await this.DeleteGetId(this.id);
          }
        },
      ];
    }

    },
    created() {
        this.ConsultarRegistros();
    },

    mounted() {
        // Definir una función asíncrona dentro del mounted
        const lockOrientation = async () => {
            try {
                if (typeof ScreenOrientation.lock !== 'undefined') {
                    await ScreenOrientation.lock({ orientation: 'landscape' });
                } else {
                    throw new Error('ScreenOrientation API not available');
                }
            } catch (err) {
                console.error("Error locking orientation:", err);
                alert("La API de orientación de pantalla no está disponible en este navegador.");
            }
        };
        lockOrientation();
    },
  
    beforeUnmount() {
      try {
        // Desbloquear la orientación al desmontar el componente
        ScreenOrientation.unlock();
      } catch (err) {
        console.error("Error unlocking orientation:", err);
      }
    },
    watch: {
    '$route'(to) {
      if (to.path === '/boardaditivos') {
        this.ConsultasDatos();
      }
    }
  }
    //boardaditivos
  };
  </script>
  
<style>
.Icon{
  margin-left: 5px;
  margin-right: 5px;
  width: 20px;
  height: 20px; 
}

.table-space {
  margin: 25px 0;
}

.pagination-row {
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  margin-top: 0px;
  margin-bottom: 10px;
}

.pagination-icon {
  display: flex;
  justify-content: center;
  align-items: center;
}

.pagination-text {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
  
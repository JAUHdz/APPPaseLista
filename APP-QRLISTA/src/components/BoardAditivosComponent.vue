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
                <h3>Tabla aditivos vaciados</h3>
              </ion-col>
            </ion-row>
            <ion-row style="margin-bottom: 15px">
              <ion-col>
                <ion-input placeholder="Username" v-model="searchName"></ion-input>
              </ion-col>
              <ion-col>
                <ion-input placeholder="Vaciado" v-model="searchVaciado"></ion-input>
              </ion-col>
              <ion-col>
                <ion-input  type="date" placeholder="Fecha" v-model="searchDate"></ion-input>
              </ion-col>
            </ion-row>
            <ion-row>
                <ion-col class="pagination-text">Usuario</ion-col>
                <ion-col class="pagination-text">Aditivo</ion-col>
                <ion-col class="pagination-text">Contenedor</ion-col>
                <ion-col class="pagination-text">Vaciado</ion-col>
                <ion-col class="pagination-text">Fecha</ion-col>
                <ion-col class="pagination-text">Ubicacion</ion-col>
                <ion-col class="pagination-text">Acciones</ion-col>
            </ion-row>
            <!-- Verificar si usuarioTabla está vacío o no -->
            <ion-row v-if="paginatedAditivos.length === 0">
              <ion-col>No hay usuarios para mostrar</ion-col>
            </ion-row>

            <ion-row v-else v-for="aditivo in paginatedAditivos" :key="aditivo.id_aditivos" class="table-space">
                <ion-col class="pagination-text">{{ aditivo.UsuarioDato ? aditivo.UsuarioDato.nom_usuario : 'N/A' }}</ion-col>
                <ion-col class="pagination-text">{{ aditivo.nom_aditivo }}</ion-col>
                <ion-col class="pagination-text">{{ aditivo.nom_contenedor }}</ion-col>
                <ion-col class="pagination-text">{{ aditivo.validacion }}</ion-col>
                <ion-col class="pagination-text">{{ aditivo.fecha }}</ion-col>
                <ion-col class="pagination-text">{{ aditivo.localizacion }}</ion-col>
                <ion-col class="pagination-text">
                  <ion-icon :icon="trashOutline" class="Icon" color="danger" @click.prevent="openalert(aditivo.id_aditivos)"></ion-icon>
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
            aditivosTabla: [],
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
      async ConsultarAditivosVaciados() {
            try {
                const responseAditivos = await fetch('https://cemexapi20240515142245.azurewebsites.net/api/Cat_Aditivos');
                this.aditivosTabla = await responseAditivos.json();
                console.log("Consulta exitosa de aditivos");
            } catch (error) {
                console.error("Error en la consulta de los Aditivos:", error);
            }
  
            try {
                const responseUsuarios = await fetch('https://cemexapi20240515142245.azurewebsites.net/api/Usu_Usuarios');
                this.usuarioTabla = await responseUsuarios.json();
                console.log("Consulta exitosa de usuarios");
            } catch (error) {
                console.error("Error en la consulta de Usuarios:", error);
            }
  
            this.aditivosTabla = this.aditivosTabla.map(aditivo => {
                aditivo.UsuarioDato = this.usuarioTabla.find(usuario => usuario.id_usuario === aditivo.id_usuusuario);  
                return aditivo;
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
        await this.ConsultarAditivosVaciados();
        event.target.complete();  
      },
      openalert(id_user){
        this.deletealert = true;
        this.id = id_user;
      },
      resetDeleteAlert() {
        this.deletealert= false;
      },

      async DeleteGetId(id_aditivo) {
      try {
        await fetch(`https://cemexapi20240515142245.azurewebsites.net/api/Cat_Aditivos?id=${id_aditivo}`, {
        method: 'DELETE'
        });
        console.log('Aditivo eliminado correctamente', id_aditivo)
        this.deletealert= false;
        this.ConsultarAditivosVaciados();
      } catch (error) {
        console.error("Error al eliminar el Aditivo:", error);
        this.deletealert= false;
      }
    },
    },
    computed: {
      
      filteredAditivos() {
        return this.aditivosTabla.filter(aditivo => {
          const matchesName = this.searchName ? (aditivo.UsuarioDato && aditivo.UsuarioDato.nom_usuario.toLowerCase().includes(this.searchName.toLowerCase())) : true;
          const matchesDate = this.searchDate ? aditivo.fecha.substring(0, 10) === this.searchDate.substring(0, 10) : true;
          const matchesVaciado = this.searchVaciado ? aditivo.validacion.toLowerCase() === this.searchVaciado.toLowerCase() : true;
          return matchesName && matchesDate && matchesVaciado;
        });
      },

      paginatedAditivos() {
        const start = (this.currentPage - 1) * this.itemsPerPage;
        const end = start + this.itemsPerPage;
        return this.filteredAditivos.slice(start, end);
      },
    
      totalPages() {
        return Math.ceil(this.filteredAditivos.length / this.itemsPerPage);
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
        this.ConsultarAditivosVaciados();
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
  
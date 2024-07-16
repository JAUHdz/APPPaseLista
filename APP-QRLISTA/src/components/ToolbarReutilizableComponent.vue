<template>
    <ion-header>
        <ion-toolbar>
            <ion-title color="primary">{{ title }}</ion-title>
            <ion-icon v-if="BackPath != null" slot="end" color="primary" class="CenterIcon" :icon="Icono" @click.prevent="redirectRegister(BackPath)"></ion-icon>
        </ion-toolbar>
        <ion-progress-bar type="indeterminate"></ion-progress-bar>
    </ion-header>
</template>

<script>
import {IonHeader, IonToolbar, IonTitle, IonProgressBar, IonIcon} from '@ionic/vue';
import { useRouter } from 'vue-router';
export default {
    name: 'ToolbarReutilizableComponent',
    components: {
        IonHeader,
        IonToolbar,
        IonTitle,
        IonProgressBar,
        IonIcon,
    },
    props: {
        title: String,
        BackPath: String,
        Icono: String,
    },
    setup() {

        const router = useRouter();
        
        const redirectRegister = (ruta) => {
            router.push(ruta);
        }
        
        return {
            redirectRegister
        };
    }
}
</script>

<style>
.CenterIcon {
    margin-right: 40px;
    width: 20px;
    height: 20px;
}
</style>


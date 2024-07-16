<template>
  <ion-card>
    <ion-card-header>
      <ion-card-title>{{ title }}</ion-card-title>
      <ion-card-subtitle>{{ subtitle }}</ion-card-subtitle>
    </ion-card-header>
    <ion-card-content>
      <p class="inline-content" @click="redirectRutaOne">
        <ion-icon
          :icon="IconoUno"
          color="primary"
        ></ion-icon>
        {{ textUno }}
      </p>
      <br v-if="textDos != null">
      <p v-if="textDos != null" class="inline-content" @click="redirectRutaTwo">
        <ion-icon
          :icon="IconoDos"
          color="primary"
        ></ion-icon>
        {{ textDos }}
      </p>
    </ion-card-content>
  </ion-card>
</template>

<script>
import {
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonCardSubtitle,
  IonCardContent,
  IonIcon,
} from '@ionic/vue';
import { useRouter } from 'vue-router';

export default {
  name: 'CardReutilizableComponent',
  props: {
    title: String,
    subtitle: String,
    textUno: String,
    textDos: String,
    IconoUno: String,
    IconoDos: String,
    RutaOne: String,
    RutaDos: String,
  },
  components: {
    IonCard,
    IonCardHeader,
    IonCardTitle,
    IonCardSubtitle,
    IonCardContent,
    IonIcon,
  },
  setup(props) {
    const router = useRouter();

    const redirectRutaOne = () => {
      router.push(props.RutaOne);
    };

    const redirectRutaTwo = () => {
      router.push(props.RutaDos);
    };

    return {
      redirectRutaOne,
      redirectRutaTwo,
    };
  },
};
</script>

<style>
.inline-content {
  display: inline-flex;
  align-items: center;
}

ion-icon{
  margin-right: 5px; 
}
</style>

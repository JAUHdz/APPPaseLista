<template>
    <home-ad-component v-if="typeUser==1"></home-ad-component> 
    <home-emp-ope-component v-if="typeUser!=1"></home-emp-ope-component> 
</template>

<script setup>
    import { ref, onMounted,defineProps } from 'vue';
    import HomeAdComponent from '../components/HomeADComponent.vue';
    import HomeEmpOpeComponent from '../components/HomeEMP_OPEComponent.vue';

    const props = defineProps({
        registerIonPage: undefined
    });
   
    const typeUser = ref(null);

    const getNameUserLogin = () => {
    try {
        const user = localStorage.getItem('User-login');
        if (user) {
        const nameUser = JSON.parse(user);
        typeUser.value = nameUser.idusucattipousuario;
        console.log("Datos obtenidos correctamente");
        } else {
        console.log("Vuelva a iniciar sesión para evitar cualquier error con la sesión iniciada");
        }   
    } catch {
        console.log("Hubo un error al obtener los datos de la sesión");
    }
    }
    onMounted(getNameUserLogin);
</script>
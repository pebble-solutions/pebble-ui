<template>
    <section class="text-center">

        <!-- Composant avatar-->
        <UserImage :name="imageName" size="user-image-xl"  @click.prevent="imgUser =! imgUser"/>

        <div class="input-group mb-3 mt-3" v-if="imgUser === false  && !formUser ">
        <input type="file" class="form-control" id="inputGroupFile02">
        <label class="input-group-text" for="inputGroupFile02">Importer</label>
        </div> 
        <!-- Fin Composant avatar-->
    
        <DisplayNameForm :displayName="displayName" />
        
        <AuthProviderInfo :provider="provider" :mail="email" :provider-icon="'bi bi-google'" v-if="provider !== 'password'" />
        <PasswordUpdate v-else></PasswordUpdate>
        
    </section>

</template>

<script>

import UserImage from '../UserImage.vue';
import AuthProviderInfo from './AuthProviderInfo.vue';
import PasswordUpdate from './PasswordUpdate.vue';
import DisplayNameForm from './DisplayNameForm.vue';

export default {

    data() {
        return {
            imgUser:true,
            infoProvider: false,
            displayName: null
        }
    },

    computed: {
        provider() {
            return this.$app.firebase_user.providerData[0].providerId;
        },

        email(){
            return this.$app.firebase_user.email;
        },

        imageName(){
            if(this.displayName === 'Pseudo Non Défini') return "?" ;
            else return this.displayName;
        },

    },

    watch: {
        formUser(formStatus) {
            if (!formStatus) {
                this.newDisplayName = "";
                this.errorDisplayName=false;
            }
        },

    },

    methods: {
        /**
         * Retourne le display name stocké sur l'instance Firebase
         * 
         * @returns {String}
         */
         getName() {
            return this.$app.firebase_user.displayName;
        }
    },

    components: { UserImage, AuthProviderInfo, PasswordUpdate, DisplayNameForm },

    beforeUnmount() {
        this.$app.clearEventListener("display-name-updated");
    },

    mounted() {
        this.displayName = this.getName();

        this.$app.addEventListener("display-name-updated", () => {
            this.displayName = this.getName();
        });
    }
}

</script>
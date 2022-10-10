<template>
    <section class="text-center">
        <UserImage :name="email" size="user-image-xl"  @click.prevent="imgUser =! imgUser"/>

        <div class="input-group mb-3 mt-3" v-if="imgUser === false  && formUser ">
        <input type="file" class="form-control" id="inputGroupFile02">
        <label class="input-group-text" for="inputGroupFile02">Importer</label>
        </div> 
    

        <a href="#!" class="d-flex lead justify-content-center align-items-center link-dark text-decoration-none py-2" v-if="formUser" @click.prevent="formUser = false">
            <strong>{{name}} </strong> 
            <i class="bi bi-pen-fill ms-2"></i>
        </a>
        
        <div class="input-group py-2" v-else>
            <input type="text" class="form-control" placeholder="{{name}}" >
            <button class="btn btn-outline-secondary bi bi-check" type="button" @click="formUser=true"></button>
            <button class="btn btn-outline-secondary bi bi-x" type="button" @click="formUser=true"></button>
        </div>

        <AuthProviderInfo :provider="provider" :mail="email" :provider-icon="'bi bi-google'" v-if="provider !== 'password'" />
        
        <div v-else>
            <div class="my-4" v-if="mdpid === 0">
                <button class="btn btn-outline-info" @click.prevent="mdpid = 1 "> Mot de passe<i class="bi bi-key-fill" style="padding:5px;"></i></button>
            </div>

            <div class="input-group py-2 " v-if="mdpid === 1">
                <input type="password" class="form-control" placeholder="Saisissez votre mot de passe actuel" aria-label="Recipient's username with two button addons">
                <button class="btn btn-outline-secondary bi bi-check" type="button" @click="mdpid=2"></button>
                <button class="btn btn-outline-secondary bi bi-x" type="button" @click="mdpid=0"></button>
                
            <!-- <div>
                <button class="btn btn-outline-secondary" @click="toggleShow"><span class="icon is-small is-right">
                <i class="fas" :class="{ 'fa-eye-slash': showPassword, 'fa-eye': !showPassword }"></i>
            </span>
            </button>
            </div> -->

            </div>

            <div class="input-group py-2" v-if="mdpid === 2">
                <input type="password" class="form-control" placeholder="Nouveau mot de passe" aria-label="Recipient's username with two button addons">
                <button class="btn btn-outline-secondary bi bi-check" type="button" @click="mdpid=0"></button>
                <button class="btn btn-outline-secondary bi bi-x" type="button" @click="mdpid=0"></button>
            </div>

            <div class="input-group py-2" v-if="mdpid === 2">
                <input type="password" class="form-control" placeholder="Confirmez le mot de passe" aria-label="Recipient's username with two button addons">
                <button class="btn btn-outline-secondary bi bi-check" type="button" @click="mdpid=0"></button>
                <button class="btn btn-outline-secondary bi bi-x" type="button" @click="mdpid=0"></button>
            </div>
        </div>

        
    </section>

</template>

<script>
import UserImage from '../UserImage.vue';
import AuthProviderInfo from './AuthProviderInfo.vue';

export default {

    data() {
        return {
            imgUser:true,
            // login_name: 'Amandine DOE',
            infoProvider: false,
            formConnexionMicrosoft : false,
            formConnexionGoogle : false,
            formConnexionLocal : false,
            formUser : true,
            mdpid : 0
            // showPassword: false,
            // password: null
        }
    },
    computed: {
        buttonLabel() {
            return (this.showPassword) ? "Hide" : "Show";
        },

        provider() {
            return this.$app.firebase_user.providerData[0].providerId;
        },

        email(){
            return this.$app.firebase_user.email;
        },

        name(){
            //if 
            return this.$app.firebase_user.providerData[0].displayName;        
        }

    },

    methods: {
        toggleShow() {
            this.showPassword = !this.showPassword;
        }
    },

    components: { UserImage, AuthProviderInfo },

    mounted() {
        console.log('firebase_user', this.$app.firebase_user);
        console.log('local_user', this.$app.local_user);
    }
}


/*
* Redimentionner une image en une image circulaire
*/

// <img src="images/logo_js.png" class="rounded-circle"/>

</script>
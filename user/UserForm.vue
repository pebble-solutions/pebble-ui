<template>
    <section class="text-center">
        <UserImage :name="imageName" size="user-image-xl"  @click.prevent="imgUser =! imgUser"/>

        <div class="input-group mb-3 mt-3" v-if="imgUser === false  && !formUser ">
        <input type="file" class="form-control" id="inputGroupFile02">
        <label class="input-group-text" for="inputGroupFile02">Importer</label>
        </div> 
    

        <a href="#!" class="d-flex lead justify-content-center align-items-center link-dark text-decoration-none py-2" v-if="!formUser" @click.prevent="formUser = !formUser">
            <template v-if="name === 'Pseudo Non Défini'"> <strong><em>{{name}} </em></strong> </template>
            <strong v-else>{{name}} </strong> 
                <i class="bi bi-pen-fill ms-2"></i>
        </a>
        
        <article v-else>
            <div class="input-group py-2">
                <input type="text" class="form-control" :placeholder="name" v-model="newDisplayName" @keydown.enter.prevent="verifDisplayName()" autocomplete="off">
                <button class="btn btn-outline-secondary bi bi-check" type="button" @click="verifDisplayName()"></button>
                <button class="btn btn-outline-secondary bi bi-x" type="button" @click="formUser=!formUser"></button>
            </div>
            
            <div class="alert alert-danger" role="alert" v-if="errorDisplayName">
                Veuillez inscrire un nom d'utilisateur 
            </div>
        </article>

        <!--<hr/>-->
        <AuthProviderInfo :provider="provider" :mail="email" :provider-icon="'bi bi-google'" v-if="provider !== 'password'" />

        
        <div v-else>
            <PasswordUpdate></PasswordUpdate>
        </div>

        <!-- <div class="input-group py-2">
            <button class="btn btn-danger" type="button" @click="deleteAccount()">Supprimer le compte</button>
        </div> -->

        
    </section>

</template>

<script>
import { getAuth, getIdToken } from '@firebase/auth';
import UserImage from '../UserImage.vue';
import AuthProviderInfo from './AuthProviderInfo.vue';
import PasswordUpdate from './PasswordUpdate.vue';

export default {

    data() {
        return {
            imgUser:true,
            infoProvider: false,
            formUser : false,
            newDisplayName: "",
            name: null,
            errorDisplayName: false
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
            if(this.name === 'Pseudo Non Défini') return "?" ;
            else return this.name;
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
        verifDisplayName(){
            if(this.newDisplayName==""){
                this.errorDisplayName = true
            }else{
                this.updateDisplayName()
            }
        },
        updateDisplayName() {

            let auth = getAuth();

            getIdToken(auth.currentUser)
            .then((idToken) => {
                // 1 : Envoyer une requête à l'API
                return this.$app.ax.post('http://localhost:3333/users/'+this.$app.firebase_user.uid, {

                    displayName: this.newDisplayName,
                },
                {
                    headers: {
                        "Authorization" : "Bearer "+idToken
                    }
                })
            })
            .then((data) => {
                console.log(data);

                // 2 : recevoir la réponse
    
                // 3 : si la réponse est valide, mettre à jour $app.firebase_user.displayName
                this.$app.firebase_user.displayName = this.newDisplayName;
    
                this.$app.dispatchEvent("display-name-updated");
    
                // 4 : fermer le formulaire
                this.formUser = false;
                this.errorDisplayName=false;
            })
            .catch(this.$app.catchError)

        },

        getDisplayName() {
            return this.$app.firebase_user.displayName === null ? "Pseudo Non Défini" : this.$app.firebase_user.displayName;
        },
        /* deleteAccount(){
            const auth = getAuth();
            const user = auth.currentUser;

            deleteUser(user).then(() => {
            // User deleted.
            }).catch((error) => {
                console.log(error);
            });
        } */
    },

    components: { UserImage, AuthProviderInfo, PasswordUpdate },

    beforeUnmount() {
        this.$app.clearEventListener("display-name-updated");
    },

    mounted() {

        this.name = this.getDisplayName();

        this.$app.addEventListener("display-name-updated", () => {
            this.name = this.getDisplayName();
        });

        console.log('firebase_user', this.$app.firebase_user);
        console.log('local_user', this.$app.local_user);
    }
}

</script>
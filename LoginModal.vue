<template>
    <div class="modal fade" :class="displayClass" id="loginModal" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="loginModalTitle" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <form class="modal-body" method="post" action="/" @submit.prevent="login">
                    <div class="text-center mb-4 mt-2">
                        <h3 class="modal-title" id="loginModalTitle">Connexion</h3>
                    </div>

                    <div class="alert alert-danger mb-3" v-if="error">{{error}}</div>

                    <div class="form-floating mb-3">
                        <input type="text" class="form-control form-control-lg" name="login" id="login_login" placeholder="name@example.com" v-model="username" required autofocus>
                        <label for="login_login">Utilisateur / e-mail</label>
                    </div>

                    <div class="form-floating mb-3">
                        <input type="password" class="form-control form-control-lg" name="password" id="login_password" placeholder="Passphrase" v-model="password" required>
                        <label for="login_password">Mot de passe</label>
                    </div>

                    <div class="d-grid gap-2">
                        <button class="btn btn-lg btn-primary" type="submit" :disabled="pending.auth">
                            <span v-if="pending.auth">
                                <span class="spinner-border spinner-border-sm" role="status"></span>
                                Authentification...
                            </span>
                            <span v-else>Connexion</span>
                        </button>
                        <a href="/" class="text-secondary text-center">Mot de passe oublié</a>
                    </div>

                    <!--<hr class="border-light">

                    <div class="d-grid gap-2">
                        <button @click="loginProvider('google')" type="button" class="btn btn-outline-secondary btn.lg"><i class="bi bi-google"></i> Connexion avec Google</button>
                    </div>-->

                    <div class="text-center mt-3 pt-3 mb-2">
                        <img src="@/components/pebble-ui/assets/pebble-dark-64.png" alt="Pebble logo" title="Pebble V" class="pebble-logo">
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>

<script>

import * as bootstrap from "bootstrap"

/**
 * Ce composant affiche une boite de dialogue afin de se connecter à un compte utilisateur.
 * Lorsque la connexion est valide, le composant renvoie l'utilisateur connecté et passe 
 * automatiquement sur la structure par défaut de l'utilisateur.
 * 
 * @param {Boolean} display Affiche ou masque la boite de dialogue
 * 
 * @event auth-change {LocalUser}
 * @event structure-change {Number}
 */
export default {
    props: {
        display: Boolean
    },

    emits: ['auth-change', 'structure-change'],

    data() {
        return {
            username: null,
            password: null,
            pending: {
                auth: false
            },
            error: null,
            modal: null
        }
    },

    methods: {
        /**
         * Retourne la classe css à utiliser sur la modal en fonction de la propriété display
         * @returns {String}
         */
        displayClass() {
            if (this.display) {
                return 'show';
            }
            return '';
        },

        /**
         * Authentification à l'API
         */
        login() {
            this.pending.auth = true;

            this.$app.login(this, this.username, this.password)
            .then((resp) => {
                this.error = null;
                this.modal.hide();

                console.log(resp);

                this.$emit('structure-change', this.$app.active_structure_id);
                this.$emit('auth-change', resp);
            })
            .catch((error) => {
                this.error = this.$app.catchError(error, {
                    mode: 'message'
                });
            })
            .finally(() => {
                this.pending.auth = false;
            });
        },

        /**
         * Authentification à l'API via un prestataire externe
         */
        loginProvider(provider)
        {
            this.$app.loginProvider(provider)
            .then((resp) => {
                console.log(resp);
            })
            .catch((error) => {
                this.error = this.$app.catchError(error, {
                    mode: 'message'
                });
            });
        }
    },

    mounted() {
        this.modal = new bootstrap.Modal(document.getElementById('loginModal'));
        this.modal.show();
    }
}
</script>

<style lang="scss" scoped>
.pebble-logo {
    max-width: 32px;
}
</style>
<template>
    <div class="modal fade" :class="displayClass" id="loginModal" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="loginModalTitle" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-body">
                    <div class="text-center mb-4 mt-2">
                        <h3 class="modal-title" id="loginModalTitle">Connexion</h3>
                    </div>
                    <div class="form-floating mb-3">
                        <input type="text" class="form-control form-control-lg" name="login" id="login_login" placeholder="name@example.com" required autofocus>
                        <label for="login_login">Utilisateur / e-mail</label>
                    </div>

                    <div class="form-floating mb-3">
                        <input type="password" class="form-control form-control-lg" name="password" id="login_password" placeholder="Passphrase" required>
                        <label for="login_password">Mot de passe</label>
                    </div>

                    <div class="d-grid gap-2">
                        <button class="btn btn-lg btn-primary" type="submit">Connexion</button>
                        <a href="/" class="text-secondary text-center">Mot de passe oublié</a>

                        <div class="text-center mt-3 pt-3 mb-2">
                            <img src="@/components/pebble-ui/assets/pebble-dark-64.png" alt="Pebble logo" title="Pebble V" class="pebble-logo">
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    props: {
        display: Boolean
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
        }
    }
}
</script>

<style lang="scss" scoped>
.pebble-logo {
    max-width: 32px;
}
</style>
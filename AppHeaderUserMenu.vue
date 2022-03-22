<!--
    Pebble menu utilisateur
    Ce composant affiche le menu utilisateur, le menu des structures et le paramétrage général

    Paramètres :
    - structures        Liste des structures chargées par l'application
    - sessLogin         Login connecté
    - cfgMenu           Paramètre du menu de configuration. Si une clé href existe, alors le menu de configuration s'affiche

    Événement emits
    - config-module     Envoie le signal d'affichage de la boite de dialogue de configuration
-->

<template>
    <ul class="nav align-items-center">
        <li class="nav-item dropdown" v-if="local_user">
            <a href="#" title="Changer de structure" class="nav-link text-light dropdown-toggle" data-bs-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false"><i class="fa fa-sitemap"></i> {{local_user.structures[0].nom_interne}}</a>
            <div class="dropdown-menu dropdown-menu-right">
                <a :href="'/mkg/private/php/structure_switch.php?structure_id='+structure.id" class="dropdown-item d-flex align-items-center justify-content-between" v-for="structure in local_user.structures" :key="structure.id">
                    {{structure.nom_interne}}
                    <i v-if="structure.isCurrentSession" class="fas fa-check text-success"></i>
                </a>
            </div>
        </li>
        <li class="nav-item dropdown" v-if="local_user">
            <a href="{RACINE_VERSION}modules/espace/private/php/menu_profile.php" :title="'@'+local_user.login.pseudo" class="d-block text-decoration-none" data-bs-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">
                <UserImage :login="local_user.login" />
            </a>
            <div class="dropdown-menu dropdown-menu-right">
                <a class="dropdown-item" href="/mkg/modules/espace/private/php/login_3_resume.php?login_id={sess_login_id}" target="espaceApp">Mon compte</a>
                <a class="dropdown-item" href="/mkg/private/php/index.php?d=OUI">Déconnexion</a>
            </div>
        </li>
        <li class="nav-item dropdown">
            <a href="#" class="nav-link text-light"  data-bs-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">
                <i class="bi bi-gear"></i>
            </a>
            <div class="dropdown-menu dropdown-menu-right">
                <a :href="cfgMenu.href" class="dropdown-item" @click.prevent="configModule()" v-if="cfgMenu.href">Configuration du module</a>
                <a href="/mkg/modules/parametre/private/php/index.php" class="dropdown-item" target="parametreApp">Configuration générale</a>
            </div>
        </li>
    </ul>
</template>

<script>

import UserImage from './UserImage.vue'

/**
 * Header menu component
 * 
 * @param {Array} structures
 * @param {Object} sessLogin
 * @param {Object} cfgMenu
 * @param {Object} activeStructure
 * 
 * @event {Void} config-module
 */
export default {
    props: {
        structures: Array,
        sessLogin: Object,
        local_user: Object,
        cfgMenu: Object,
        activeStructure: Object
    },

    components: {
        UserImage
    },
    
    methods: {
        // Config module
        configModule() {
            this.$emit('config-module');
        } // End of config module
    }
}
</script>

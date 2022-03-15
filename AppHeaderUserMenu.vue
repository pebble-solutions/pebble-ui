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
        <li class="nav-item dropdown" v-if="activeStructure">
            <a href="#" title="Changer de structure" class="nav-link text-light dropdown-toggle" data-bs-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false"><i class="fa fa-sitemap"></i> {{activeStructure.nom_interne}}</a>
            <div class="dropdown-menu dropdown-menu-right">
                <a :href="'/mkg/private/php/structure_switch.php?structure_id='+structure.id" class="dropdown-item d-flex align-items-center justify-content-between" v-for="structure in structures" :key="structure.id">
                    {{structure.nom_interne}}
                    <i v-if="structure.isCurrentSession" class="fas fa-check text-success"></i>
                </a>
            </div>
        </li>
        <li class="nav-item dropdown" v-if="sessLogin">
            <a href="{RACINE_VERSION}modules/espace/private/php/menu_profile.php" title="{sess_login}" class="d-block text-light" data-bs-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">
                <span class="d-block" :style="'background-image:url('+sessLogin.oAvatar.url_root+');background-size:cover;background-position:center; width:32px; height:32px; border-radius:50%;'" v-if="sessLogin.oAvatar"></span>
                <span class="text-center d-block" :style="'line-height: 32px; min-width: 32px; min-height: 32px; background-color:'+sessLogin.login.toColor()+'; width:32px; height:32px; border-radius: 50%; color: white;'" v-else>
                    {{sessLogin.login[0].toUpperCase()}}
                </span>
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

import stc from 'string-to-color'

String.prototype.toColor = function() {
    return stc(this);
};

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
        cfgMenu: Object,
        activeStructure: Object
    },
    
    methods: {
        // Config module
        configModule() {
            this.$emit('config-module');
        } // End of config module
    }
}
</script>

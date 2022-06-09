<!--
    App wrapper
    Ce composant affiche la structure générale de l'application.

    Paramètres :
    - cfg               Paramètre de configuration de l'application
    - cfgMenu           Paramètre du menu de configuration. Si une clé href existe, alors le menu de configuration s'affiche
    - cfgSlots          Paramètre d'affichage des slots (éléments)

    Événement emits
    - menu-toggle       Envoie le signal d'inversion du menu de l'application
    - config-module     Envoie le signal d'affichage de la boite de dialogue de configuration
    - auth-change       Mise à jour de l'authentification de l'utilisateur
-->

<template>
    <div class="row g-0 bg-dark sticky-top text-light align-items-center border-bottom border-secondary" :class="{'filter-blur' : !local_user}" id="app-header" style="z-index: 1025;">
        <AppHeaderMenu
            :cfg="cfg"
            :cfg-menu="cfgMenu"
            :local_user="local_user"

            @menu-toggle="menu = !menu"
            @config-module="$emit('config-menu')" />

        <div class="col d-flex align-items-center justify-content-between">
            <div>
                <slot name="header" v-if="slots.header"></slot>
            </div>

            <!-- Barre d'outil ITCloud -->
            <div>
                <AppHeaderUserMenu
                    :cfg-menu="cfgMenu"
                    :local_user="local_user"
                    :active_structure_id="active_structure_id"

                    @config-module="$emit('config-menu')"
                    @storage-modal="storageModal = true"
                    @structure-change="activateStructure" />
            </div>
            <!-- Fin de la barre d'outil IT Cloud -->
        </div>
    </div>

    <div class="row g-0" :class="{'filter-blur' : !local_user}">
        <div class="col-3 border-end overflow-auto scrollbar sidebar-full-height sticky-top" :class="{'bg-dark text-light': menu, 'bg-light': !menu}" id="app-list" :style="'padding-left:'+paddingLeft+';'" v-if="slots.menu || slots.list">
            <slot name="menu" v-if="menu && slots.menu"></slot>
            <slot name="list" v-else-if="!menu && slots.list"></slot>
        </div>

        <div class="col" v-if="slots.core">
            <slot name="core"></slot>

            <div class="app-footer">
                <div class="mb-2">
                    <img src="@/components/pebble-ui/assets/pebble-dark-64.png" title="Work less, do more" alt="Pebble" style="max-width: 48px;">
                </div>
                <small class="d-block">Pebble  Version 5</small>
            </div>
        </div>
    </div>

    <LoginModal v-if="!local_user" @auth-change="setLocal_user" @structure-change="setActiveStructureId" />

    <StorageModal :display="storageModal" @modal-hide="storageModal = false" @modal-show="storageModal = true" />
</template>

<script>

import AppHeaderMenu from './AppHeaderMenu.vue'
import AppHeaderUserMenu from './AppHeaderUserMenu.vue'
import LoginModal from './LoginModal.vue'
import StorageModal from './StorageModal.vue'

import {getAuth, onAuthStateChanged} from 'firebase/auth'

/**
 * Application wrapper component
 *
 * @param {Object} cfg
 * @param {Object} cfgMenu
 * @param {Object} cfgSlots
 *
 * @event menu-toggle {Boolean}
 * @event config-module {Void}
 * @event auth-change {Object}
 */

export default {
    props: {
        cfg: Object,
        cfgMenu: Object,
        cfgSlots: Object
    },

    emits: ['auth-change', 'menu-toggle', 'config-module', 'structure-change'],

    data() {
        return {
            pending: {
                structures: true,
                sessLogin: true
            },
            menu: false,
            slots: {
                menu: true,
                list: true,
                core: true,
                header: true
            },
            local_user: null,
            active_structure_id: null,
            storageModal: false
        }
    },

    components: {
        // Chargement des dépenses externes
        AppHeaderMenu,
        AppHeaderUserMenu,
        LoginModal,
        StorageModal
    },

    watch: {
        /**
         * menu(val)
         * Observe le changement d'état du menu
         * @param {Bool} val
         *
         * @event menu-toggle {Bool}
         */
        menu(val) {
            this.$emit('menu-toggle', val);
        }
        // EO menu()
    },

    computed: {
        /**
         * paddingLeft()
         * Retourner l'espace à gauche si aside est à true
         * @returns {String}
         */
        paddingLeft() {
            if (this.cfg.aside) {
                return '52px';
            }
            else {
                return '0px';
            }
        },
        // EO paddingLeft()
    },

    methods: {
        /**
         * resize()
         * This function calculate the size of interface element on application load,
         * view update or browser resize
         */
        resize() {
            let header = document.getElementById('app-header');
            let aside = document.getElementById('app-list');

            let win_height = window.innerHeight;
            let header_height = header.offsetHeight;

            let height = win_height - header_height;

            if (header) {
                aside.style.height = height+'px';
                aside.style.top = header_height+'px';
            }
        },

        /**
         * Met à jour l'utilisateur local
         * @param {Object} user
         */
        setLocal_user(user) {
            this.local_user = user;
            this.$emit('auth-change', user);
        },

        /**
         * Met à jour la structure active
         * @param {Integer} structureId
         */
        setActiveStructureId(structureId) {
            this.active_structure_id = structureId;
            this.$emit('structure-change', structureId);
        },

        /**
         * Demande de changer de structure
         * @param {Integer} structureId
         */
        activateStructure(structureId) {
            this.$app.setStructure(structureId);
            this.setActiveStructureId(structureId);
        }
    },
    
    mounted() {

        for (const key in this.cfgSlots) {
            this.slots[key] = this.cfgSlots[key];
        }

        let auth = getAuth(this.$app.firebaseApp);

        onAuthStateChanged(auth, (user) => {

            console.log(user);

            this.$app.authToApi(user)
            .then((local_user) => {
                console.log(local_user);
                this.setActiveStructureId(this.$app.active_structure_id);
                this.setLocal_user(local_user);
            });

        });

        this.resize();

        window.addEventListener('resize', () => {
            this.resize();
        });

    }
};
</script>


<style lang="scss">

@use "sass:color";
@import "@/_variables.scss";

.apps-menu-btn {
    padding:10px 12px;
    background-color: $theme-color;
}

.apps-menu-btn:hover {
    background-color: darken($theme-color, 10%);
}

.apps-menu-icon {
    display: inline-block;
    min-width:28px;
    min-height:28px;
    vertical-align: middle;
    /*background:#ffffff;
    mask-size:cover;
    -webkit-mask-size:cover;*/
}

.apps-menu-icon-apps {
    /*mask: url('/mkg/images/svg/application-mobile.svg');
    -webkit-mask: url('/mkg/images/svg/application-mobile.svg');*/
    background-image:url('assets/pebble-white-64.png');
    background-position: center;
    background-size: contain;
    background-repeat: no-repeat;
}

.apps-menu-icon-back {
    mask: url('/mkg/images/svg/left-arrow.svg');
    -webkit-mask: url('/mkg/images/svg/left-arrow.svg');
    background:#ffffff;
    mask-size:cover;
    -webkit-mask-size:cover;
}

.apps-menu-sidebar {
    position:fixed; 
    left:0px; 
    width:52px; 
    bottom:0px; 
    top:48px;
    border-top:1px solid darken($theme-color, 8%);
    transition: width .3s, top .1s;
    z-index: 1030;
    background-color:$theme-color;
}

.apps-menu-sidebar .app-label {
    display:none;
}

.apps-menu-sidebar.active {
    width:100%;
    top:0px;
    border-top-width: 0px;
    overflow: auto;
}

.apps-menu-sidebar.active .app-label {
    display:inline;
}

.apps-menu-sidebar-logo {
    position:fixed;
    bottom:0px;
    left:0px;
    width:52px;
    box-sizing: border-box;
    padding:15px;
}

.apps-menu-sidebar-items {
    margin:10px 0px;
    padding:0px;
}

.apps-menu-sidebar-items + .apps-menu-sidebar-items {
    border-top:1px solid rgba(0,0,0,0.2);
    padding-top:10px;
}

.apps-menu-border {
    border-color: rgba(0,0,0,0.2) !important;
}

.apps-menu-sidebar-items.horizontal {
    display:flex;
    flex-wrap: wrap;
    align-items: flex-start;
}

.apps-menu-sidebar-items.horizontal .apps-menu-sidebar-el {
    min-width:90px;
    max-width:130px;
    flex:1;
    text-align: center;
}

.apps-menu-sidebar-items.horizontal .app-icon,
.apps-menu-sidebar-items.horizontal .app-label {
    display:block;
    margin:8px auto;
}

.apps-menu-sidebar-el {
    padding:0px;
    margin:0px;
    list-style-type: none;
}

.apps-menu-sidebar-item {
    display:block;
    padding:10px 0px;
    text-decoration: none;
    text-transform: capitalize;
}

.apps-menu-sidebar-item:hover {
    text-decoration: none;
}

.apps-menu-sidebar-item .app-icon {
    text-align: center;
    width:52px;
}

.apps-menu-sidebar-item:hover,
.apps-menu-sidebar-item.active {
    background-color: rgba(0,0,0,0.1);
}

.apps-launcher {
    position: fixed;
    left:380px;
    right:0px;
    bottom:0px;
    top:48px;
}

.text-admin {
    color:#a012ff;
}

.btn-admin {
    background-color: #a012ff;
    border-color: #6f09b3;
    color:white;
}

.btn-admin:hover {
    background-color: #8a10db;
    border-color: #60099b;
    color:white;
}

.btn-outline-admin {
    border-color: #a012ff;
    color:#a012ff;
}

.btn-outline-admin:hover {
    border-color: #a012ff;
    color:white;
    background-color: #8a10db;
}

.btn-admin:focus,
.btn-outline-admin:focus {
    box-shadow: 0 0 0 0.2rem rgba(160, 18, 255, 0.5);
}

.app-footer {
    opacity:0.5;
    margin:20px auto;
    text-align: center;
    transition: opacity .1s;
}

.app-footer:hover {
    opacity:0.6;
}

.alert-admin {
    color:#655074;
    background-color: #f2e9f7;
    border-color: #b49cc4;
}

/**
 * Dropzone style
 */

 .dropzone-wrapper {
    margin:15px 0px;
 }

.dropzone {
	border: 2px dashed rgb(212, 212, 212);
	color:rgb(100,100,100);
	border-radius: 5px;
	background-color: rgb(250,250,250);
    min-height: auto;
    padding:8px 15px;
    margin-bottom:4px;
}

.dropzone .dz-message {
    margin:0px;
}

.dropzone:hover {
	background-color:rgb(245,245,245);
	border-color: rgb(180,180,180);
}

.dropzone-dark {
	background-color:rgb(80,80,80);
	border-color: rgb(50,50,50);
	color:rgb(150,150,150);
}

.dropzone-dark:hover {
	background-color:rgb(70,70,70);
	border-color: rgb(40,40,40);
}

.filter-blur {
    filter: blur(8px);
    -webkit-filter: blur(8px);
}
</style>
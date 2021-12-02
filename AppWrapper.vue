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
    - loaded-structures Envoie le signal de fin de chargement des structures  avec la liste des structures en argument
    - loaded-sess-login Envoie le signal de fin de chargement du login avec le login chargé en argument
-->

<template>
    <div class="row no-gutters bg-dark sticky-top text-light align-items-center border-bottom border-secondary" id="app-header" style="z-index: 1025;">
        <AppHeaderMenu
            :cfg="cfg"
            :structures="structures"
            :sess-login="sessLogin"
            :cfg-menu="cfgMenu"

            @menu-toggle="menu = !menu"
            @config-module="$emit('config-menu')"

            v-if="structures && sessLogin" />

        <div class="col d-flex align-items-center justify-content-between">
            <div>
                <slot name="header" v-if="slots.header"></slot>
            </div>

            <!-- Barre d'outil ITCloud -->
            <div>
                <AppHeaderUserMenu
                    :structures="structures"
                    :sess-login="sessLogin"
                    :cfg-menu="cfgMenu"

                    @config-module="$emit('config-menu')"

                    v-if="structures && sessLogin" />
            </div>
            <!-- Fin de la barre d'outil IT Cloud -->
        </div>
    </div>

    <div class="row no-gutters">
        <div class="col-3 border-right overflow-auto scrollbar sidebar-full-height sticky-top" :class="{'bg-dark text-light': menu, 'bg-light': !menu}" id="app-list" :style="'padding-left:'+paddingLeft+';'" v-if="slots.menu || slots.list">
            <slot name="menu" v-if="menu && slots.menu"></slot>
            <slot name="list" v-else-if="!menu && slots.list"></slot>
        </div>

        <slot name="core" v-if="slots.core"></slot>
    </div>
</template>

<script>

import AppHeaderMenu from 'AppHeaderMenu.vue'
import AppHeaderUserMenu from 'AppHeaderUserMenu.vue'

/**
 * Application wrapper component
 *
 * @param {Object} cfg
 * @param {Array} structures
 * @param {Object} sessLogin
 * @param {Object} cfgMenu
 *
 * @event menu-toggle {Boolean}
 * @event config-module {Void}
 * @event loaded-structures {Object}
 * @event loaded-sess-login {Object}
 */

export default {
    props: {
        cfg: Object,
        cfgMenu: Object,
        cfgSlots: Object
    },

    data() {
        return {
            pending: {
                structures: true,
                sessLogin: true
            },
            structures: null,
            sessLogin: null,
            menu: false,
            slots: {
                menu: true,
                list: true,
                core: true,
                header: true
            }
        }
    },

    components: {
        // Chargement des dépenses externes
        AppHeaderMenu,
        AppHeaderUserMenu
    },

    watch: {
        /**
         * structures(val)
         * Lors du chargement des structures, un événement est émit contenant l'objet chargé
         *
         * @param {Object} val
         *
         * @event loaded-structures {Object}
         */
        structures(val) {
            this.$emit('loaded-structures', val);
        },
        // EO structures()


        /**
         * sessLogin(val)
         * Lors du chargement du login connecté, un événement est émit contenant l'objet chargé
         *
         * @param {Object} val
         *
         * @event loaded-sess-login {Object}
         */
        sessLogin(val) {
            this.$emit('loaded-sess-login', val);
        },
        // EO sessLogin()


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
            if (this.cfg.aside || this.cfg.app_mode !== 'standalone') {
                return '52px';
            }
            else {
                return '0px';
            }
        }
        // EO paddingLeft()
    },
    
    mounted() {

        for (const key in this.cfgSlots) {
            this.slots[key] = this.cfgSlots[key];
        }

        query = {
            login:'self'
        };

        MKGGet.queue.push({
            url: '/api/structure/GET/list/'+JSON.stringify(query),
            self: this,
            callback(resp, self) {
                if (resp.status === 'OK') {
                    self.structures = resp.data;
                }
                // Erreur dans la réponse
                else {
                    alert('Erreur : '+resp.message);
                    console.error(resp);
                }
                self.pending.structures = false;
            }
        });

        MKGGet.queue.push({
            url: '/api/login/GET/SESSION',
            self: this,
            callback(resp, self) {
                if (resp.status === 'OK') {
                    self.sessLogin = resp.data;
                }
                // Erreur dans la réponse
                else {
                    alert('Erreur : '+resp.message);
                    console.error(resp);
                }

                self.pending.sessLogin = false;
            }
        });

    }
}
</script>
<template>
    <div class="col-3 border-secondary d-flex align-items-center pebble-rod-menu" :class="{'border-end' : cfgSlots.menu || cfgSlots.list}" :style="'max-width:'+mobileMenuSize+';'">
        <a class="apps-menu-btn me-3" href="#!" title="Retour" v-if="cfg.app_mode == 'standalone' || cfg.backNavigation" @click.prevent="actionBack()">
            <i class="bi bi-arrow-left text-white fs-5"></i>
        </a>

        <a class="apps-menu-btn me-3" href="/admin" title="Toutes les applications" @click.prevent="appsLauncher = !appsLauncher" v-else-if="cfg.app_mode != 'standalone' && cfg.ppp == 'private'">
            <i v-if="cfg.logos" class="apps-menu-icon"><img :src="getLogo()" alt="Pebble"></i>
            <i v-else class="apps-menu-icon apps-menu-icon-apps"></i>
        </a>

        <span class="apps-menu-btn me-3" href="/admin" title="Toutes les applications" v-else>
            <i v-if="cfg.logos" class="apps-menu-icon"><img :src="getLogo()" alt="Pebble"></i>
            <i v-else class="apps-menu-icon apps-menu-icon-apps"></i>
        </span>


        <a href="#!" class="navbar-brand text-light " v-if="burgerMenu" @click.prevent="menu = !menu" title="Menu application">
            <i class="bi bi-x-lg me-1" style="width:22px; text-align: center;" v-if="menu"></i>
            <i class="bi bi-list me-1" style="width:22px; text-align: center;" v-else></i>
            {{cfg.moduleLabel}}
        </a>
        <span class="navbar-brand text-light " v-else>
            {{cfg.moduleLabel}}
        </span>

        <div class="apps-menu-sidebar" :class="{active : appsLauncher}" v-if="cfg.aside">
            <div v-if="!pending.modules && modules && cfg.app_mode != 'standalone'">

                <div class="d-flex justify-content-between align-items-center">
                    <div class="navbar-brand text-light d-flex align-items-center"  v-if="appsLauncher">
                        <a href="#!" class="px-3 py-1 text-light" @click.prevent="appsLauncher = !appsLauncher"><i class="fas fa-arrow-left"></i></a>
                        Mes applications
                    </div>
                    <AppHeaderUserMenu 
                        :structures="structures" 
                        :sess-login="sessLogin" 
                        :cfg-menu="cfgMenu"
                        :active-structure="activeStructure"
                        v-on:config-module="$emit('config-module')"
                        v-if="appsLauncher" />
                </div>

                <div class="row no-gutters mt-1">
                    <div :class="{'col-3 border-end apps-menu-border sticky-top': appsLauncher}">
                        <div class="px-3" v-if="appsLauncher">
                            <h3>Recommandées</h3>
                            <div>Dans la même catégorie que {{activeGroupName}}</div>
                        </div>

                        <ul class="apps-menu-sidebar-items" v-if="activeModule">
                            <AppHeaderMenuItem :module="activeModule" :cfg="{active:true}" :key="activeModule.module" />
                        </ul>

                        <ul class="apps-menu-sidebar-items" v-if="activeGroup">
                            <AppHeaderMenuItem v-for="module in activeGroup.filter(e => e.module != cfg.module)" :module="module" :cfg="{}" :key="module.module" />
                        </ul>
                    </div>

                    <div class="col-3 border-end apps-menu-border" v-if="appsLauncher">
                        <div class="px-3">
                            <h3>Autres applications</h3>

                            <div v-for="(modulesLs, groupName) in unactiveModulesGroups" :key="groupName">
                                <h4>{{groupName}}</h4>

                                <ul class="apps-menu-sidebar-items horizontal">
                                    <AppHeaderMenuItem v-for="module in modulesLs" :module="module" :cfg="{}" :key="module.module" />
                                </ul>
                            </div>
                        </div>
                    </div>

                    <div class="col" v-if="appsLauncher">
                        <div class="px-3">
                            <input type="text" class="form-control form-control-lg" name="apps_launcher_search" id="apps_launcher_search" placeholder="Saisissez le nom d'un module" v-model="search_keyword" v-on:keyup.enter="launchFistResult">

                            <div v-if="search_keyword">

                                <div class="alert alert-info my-1" v-if="!search_results.modules.length">
                                    Aucun module trouvé.
                                </div>

                                <ul class="apps-menu-sidebar-items horizontal">
                                    <AppHeaderMenuItem v-for="(module, i) in search_results.modules.slice(0,3)" :module="module" :cfg="{i, provider: 'search'}" :key="module.module" />
                                </ul>
                            </div>
                        </div>
                    </div>

                </div>
            </div>

            <div class="apps-menu-sidebar-logo">
                <img src="@/components/pebble-ui/assets/pebble-text-v-white.png" class="img-fluid" alt="Pebble">
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
    .pebble-rod-menu {
        width: 100%!important;
        max-width: 350px;
    }

    .pebble-burger-menu {
        display: block;
    }

    @media (min-width: 1024px) {
        .pebble-rod-menu {
            width: initial;
        }

        .pebble-burger-menu {
            display : none;
        }
    }

</style>

<script>

import AppHeaderMenuItem from './AppHeaderMenuItem.vue'
import AppHeaderUserMenu from './AppHeaderUserMenu.vue'

/**
 * Header menu component
 * 
 * @param {Object} cfg
 * @param {Object} cfgMenu
 * @param {Object} cfgSlots
 * @param {Object} localUser
 * 
 * @event {Boolean} menu-toggle
 * @event {Void} config-module
 */
export default {
    props: {
        cfg: Object,
        cfgMenu: Object,
        cfgSlots: Object,
        local_user: Object
    },

    data() {
        return {
            menu: false,
            modules: null,
            pending: {
                modules: true
            },
            appsLauncher: false,
            launchingModule: null,
            search_keyword: null,
            search_results: {
                modules: []
            },
            winWidth : 0
        }
    },    
    watch: {
        /**
         * Contrôle l'affichage du menu
         * @param {bool} val 
         */
        menu(val) {

            this.$emit('menu-toggle', val);

        },

        /**
         * Contrôle de l'affichage du launcher
         * @param {bool} val
         */
        appsLauncher(val) {

            let body = document.body;

            if (val) {
                body.style.overflow = 'hidden';
            }
            else {
                body.style.overflow = 'auto';
            }

        },

        /**
         * Lance une recherche unifiée
         * @param {string} val 
         */
        search_keyword(val) {

            this.search(val);

        }
    },

    computed: {
        /**
         * modulesGroups : renvoie la liste des modules classés par groupes
         * @return {Object}
         */
        modulesGroups() {

            let groups = {};

            for (let moduleName in this.modules) {
                let moduleConfig = this.modules[moduleName];
                if (moduleConfig.groupe) {
                    if (!(moduleConfig.groupe in groups)) {
                        groups[moduleConfig.groupe] = [];
                    }

                    groups[moduleConfig.groupe].push(moduleConfig);
                }
            }

            return groups;

        },
        // EO modulesGroups()

        /**
         * activeGroup : renvoie le groupe actif par rapport au module chargé
         * @return {Object}
         */
        activeGroup() {

            let group = this.activeGroupName;
            return this.modulesGroups[group];

        },
        // EO activeGroup()


        /**
         * activeGroupName : renvoie le nom du groupe actif
         * @return {String}
         */
        activeGroupName() {

            if (!this.pending.modules) {
                let module = this.modules[this.cfg.module];
                return module.groupe;
            }

            return null;

        },
        // EO activeGroup()


        /**
         * unactiveModulesGroups : retourne la liste des groupes inactifs
         * @return {Object}
         */
        unactiveModulesGroups() {
            let groups = {};

            for (const groupName in this.modulesGroups) {
                if (groupName !== this.activeGroupName) {
                    groups[groupName] = this.modulesGroups[groupName];
                }
            }

            return groups;
        },


        /**
         * activeModule : renvoie les informations sur le module actif
         */
        activeModule() {

            if (!this.pending.modules) {
                return this.modules[this.cfg.module];
            }

            return null;

        },
        // EO activeModule()


        /**
         * aside : retourne dans les cas suivants
         * - cfg.aside == true
         * - typeof cfg.aside == undefinded
         */
        aside() {
            if (this.cfg.aside || typeof this.cfg.aside === 'undefined') {
                return true;
            }
            return false;
        },
        // EO aside

        /**
         * retourn true si l'ecran est inferieur a 1024px et que Menu ou List est a true
         * @returns {Boolean}
         */
        burgerMenu() {
            if(this.winWidth < 1024 && (this.cfgSlots.menu || this.cfgSlots.list)) {
                return true;
            }
            return false;
        },

        /**
         * paddingLeft()
         * Retourner l'espace à gauche si aside est à true
         * @returns {String}
         */
        mobileMenuSize() {
            if(this.winWidth < 1024) {
                if(this.cfg.aside) {
                    return '312px';
                }
                return '260px';
            } else {
                if(this.cfg.aside) {
                    return '402px';
                } else {
                    return '350px';
                }
            }

            //return '350px';
        },
        // EO paddingLeft()


    },

    components: {
        AppHeaderMenuItem,
        AppHeaderUserMenu
    },

    methods: {
        /**
         * closeWindow()
         * Ferme l'onglet actif
         */
        closeWindow() {
            window.close();
        },
        // EO closeWindow()

        /**
         * Lance l'action de retour lors du clique sur la flèche retour
         */
        actionBack() {
            console.log(this.cfg.backAction);
            if (this.cfg.backAction) {
                this.$router.push(this.cfg.backAction);
            }
            else {
                this.closeWindow();
            }
        },


        /**
         * search(val)
         * Cette fonction lance une recherche unifiée, liste les références liées 
         * au mot clé dans search_results.
         * @param {string} val 
         */
        search(val) {

            let reg = new RegExp(val, 'iu');

            this.search_results.modules = [];

            // La boucle suivante masque les items de modules si ils ne contiennent pas 
            // le mot clé.
            for (const key in this.modules) {
                let m = this.modules[key];

                if (m.name.match(reg) || m.module.match(reg)) {
                    m.search_result = true;
                    this.search_results.modules.push(m);
                }
                else {
                    m.search_result = false;
                }
            }

        },
        // EO search()

        
        /**
         * launchFistResult()
         * Lance automatiquement le premier résultat de la recherche.
         * Si la recherche est vide ou si il n'y a pas de module retournée, aucune action n'est lancée.
         */
        launchFistResult() {
            if (this.search_keyword) {
                let module = this.search_results.modules[0];

                if (module) {
                    document.location.href = '/mkg/modules/'+module.module+'/private/php/';
                }
            }

        },
        // EO launchFistResult()


        /**
         * Retourne le chemin vers le logo de l'application.
         * Si cfg.logos.small existe, alors, c'est ce chemin qui sera utilisé. Dans le cas contraire, c'est cfg.logos.default
         * qui sera retourné
         * 
         * @returns {String} Le chemin vers l'image
         */
        getLogo() {
            if(this.cfg.logos.small) {
                return this.cfg.logos.small;
            } else if(this.cfg.logos.default) {
                return this.cfg.logos.default;
            } else {
                return '';
            }
        },
    },
    updated() {
        if(this.appsLauncher) {
            let input = document.getElementById('apps_launcher_search');
            input.focus();

            let sidebar = document.querySelector('.apps-menu-sidebar');
            sidebar.scrollTo(0,0);
        }
    },
    mounted() {
        this.winWidth = window.innerWidth;

        window.addEventListener('resize', () => {
            this.winWidth = window.innerWidth;
        });
    }
}
</script>

<!-- 
    Pebble application item
    Ce composant permet d'afficher un item de menu

    Paramètres :
    - module            Configuration du module

    Événement emits
    - launch            module
-->
<template>
    <li class="apps-menu-sidebar-el">
        <a :href="'/mkg/modules/'+module.module+'/private/php/'" class="apps-menu-sidebar-item text-light rounded" :class="{'active' : cfg.active, 'bg-primary border border-primary': firstSearchResult}" :title="module.name">
            <i :class="module.faIcon+' app-icon'"></i>
            <span class="app-label">{{module.name}}</span>
            <small class="d-block" v-if="firstSearchResult"><em>Touche Entrée pour lancer</em></small>
        </a>
    </li>
</template>

<script>

/**
 * Header menu component
 * 
 * @param {Object} module
 * @param {Object} cfg
 */
export default {
    props: {
        module: Object,
        cfg: Object
    },
    
    computed: {
        /**
         * firstSearchResult()
         * Retourne True si c'est le premier résultat de la recherche.
         * @returns {Boolean}
         */
        firstSearchResult() {
            return (this.cfg.i == 0 && this.cfg.provider == 'search');
        }
    }
}

</script>

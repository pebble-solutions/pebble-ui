<!--
    App search bar
    Ce composant affiche les outils de recherche à effectuer sur une liste.

    Paramètres :
    - showFilter        {Boolean}       Si true, on indique au parent qu'on veux afficher le formulaire filtre
    - searchValue       {String}        Valeur de la recherche, automatiquement mise à jour au parent via un emit
                                        pour effectuer le filtre sur la liste.
    - nbFilterActive    {Number}        Nombre d'élements actif dans le formulaire filtre
    - filterOptions     {Boolean}       Default true. Si true affiche le button de filtre

    Emitter :
    - update:showFilter                 Permet la mise à jour de la valeur showFilter du parent
    - update:searchValue :              Permet la mise à jour de la valeur searchValue du parent

-->

<template>

    <div class=" d-flex">
        <div class="input-group m-2">
            <input  type="text" class="form-control" placeholder="rechercher" :value="searchValue" @input="eventValue = $event.target.value">
            <!-- @input="$emit('update:searchValue', $event.target.value)" -->

            <button class="btn btn-outline-secondary input-group-text" type="submit" @click="updateSearchValue()">
                <i class="bi bi-search"></i>
            </button>

            <button v-if="filterOptions" @click.prevent="$emit('update:showFilter',!this.showFilter)" type="button" class="btn input-group-text" :class="filterButtonClass" >
                <i class="bi bi-funnel-fill"></i> 
                <span v-if="nbFilterActive">{{nbFilterActive}}</span> 
            </button>
        </div>
    </div>
    
</template>


<script>

export default {
    name: 'searchBar',

    props: {
        /** v-model props */
        showFilter: Boolean,
        searchValue: String,

        /**basic props */
        nbFilterActive: Number,
        filterOptions: {
            type: Boolean,
            default: true
        }
    },

    data() {
        return {
            eventValue: null,
        }
    },

    emits: ['update:showFilter', 'update:searchValue'],

    computed: {
        /**
         * Retourne la classe CSS à donner au boutton de filtre. Si des options de recherche sont actives
         * alors le bouton passe en orange.
         * @returns {String}
         */
        filterButtonClass() {
            let type = this.nbFilterActive ? 'warning' : 'secondary';
            let outline = this.nbFilterActive ? '': 'outline-';

            return 'btn-'+outline+type;
        }
    },

    methods: {
        /**
         * Renvoi searchValue pour Met a jour la variable au niveau du parent
         * 
         * @event searchValue
         */
        updateSearchValue() {
            this.$emit('update:searchValue', this.eventValue);
        }
    },
}

</script>
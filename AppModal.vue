<!--
    App modal
    Affiche les boites de dialogues pour les applications

    Paramètres :
    - id                        Id unique (sera concaténé avec Modal)
    - title                     Titre de la boite de dialogue
    - cancel-btn                Si true, affiche un bouton Annuler
    - close-btn                 Si true, affiche un bouton Fermer
    - submit-btn                Si true, affiche un bouton Enregistrer
    - delete-btn                Si true, affiche un bouton Enregistrer
    - pending                   Si true, l'enregistrement est en cours

    Événements émits :
    - submit                    Événement envoyé au parent lorsque le formulaire est soumis
    - delete                    Événement envoyé au parent lorsque le boutton suppression est cliqué
-->

<template>
    <div class="modal fade" :id="id+'Modal'" tabindex="-1" :aria-labelledby="id+'ModalLabel'" aria-hidden="true">
        <div class="modal-dialog">
            <form method="post" @submit.prevent="$emit('submit')" class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" :id="id+'ModalLabel'">{{title}}</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <slot></slot>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-outline-danger" v-if="deleteBtn" @click="deleteData()"><i class="fas fa-trash-alt"></i></button>
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" v-if="cancelBtn">Annuler</button>
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" v-if="closeBtn">Fermer</button>
                    <button type="submit" class="btn btn-primary" :disabled="pending" v-if="submitBtn">
                        <span v-if="pending">
                            <span class="spinner-border spinner-border-sm" role="status"></span>
                            Enregistrement...
                        </span>
                        <span v-else>Enregistrer</span>
                    </button>
                </div>
            </form>
        </div>
    </div>
</template>

<script>

import * as bootstrap from "bootstrap"

/**
 * Application modal component
 *
 * @param {String} id
 * @param {String} title
 * @param {Boolean} cancelBtn
 * @param {Boolean} closeBtn
 * @param {Boolean} submitBtn
 * @param {Boolean} deleteBtn
 * @param {Boolean} pending
 *
 * @event submit {void}
 * @event delete {void}
 */
export default {
    props: {
        id: String,
        title: String,
        cancelBtn: Boolean,
        closeBtn: Boolean,
        submitBtn: Boolean,
        deleteBtn: Boolean,
        pending: Boolean
    },

    data() {
        return {
            modal: null
        }
    },
    
    methods: {
        /**
         * deleteData()
         * Lance le signal de suppresion si la boite de dialogue est confirmée
         */
        deleteData() {
            let c = confirm('Souhaitez-vous supprimer cette information ?');
            if (c) {
                this.$emit('delete');
            }
        }
        // EO deleteData()
    },

    /**
     * Avant de retirer le composant, la modale doit être masquée afin de supprimer 
     * tous les éléments html créés par bootstrap
     */
    beforeUnmount() {
        if (this.modal) {
            this.modal.hide();
        }
    },
    
    /**
     * Une fois l'élément monté, on affiche la modale.
     */
    mounted() {
        this.modal = new bootstrap.Modal(document.getElementById(this.id+'Modal'));
        this.modal.show();
    }
}

</script>

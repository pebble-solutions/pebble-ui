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
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    <slot></slot>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-outline-danger" v-if="deleteBtn" @click="deleteData()"><i class="fas fa-trash-alt"></i></button>
                    <button type="button" class="btn btn-secondary" data-dismiss="modal" v-if="cancelBtn">Annuler</button>
                    <button type="button" class="btn btn-secondary" data-dismiss="modal" v-if="closeBtn">Fermer</button>
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
    
    mounted() {
        //let self = this;

        // Lors de la fermeture du modal, on vérifié que la barre d'adresse est actualisée
        // Ce code doit être revu, jQuery n'est pas importé
        /*$('#'+this.id+'Modal').on('hidden.bs.modal', function (event) {
            if (self.$root.openedElement) {
                document.location.hash = '#!'+self.$root.appName+'/'+self.$root.openedElement.id
            }
            else {
                document.location.hash = '#!'+self.$root.appName;
            }
        });*/
    }
}

</script>

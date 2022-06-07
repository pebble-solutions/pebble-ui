<template>
    <div class="alert text" :class="classList()" role="alert">
        <i v-if="icon" class="m-2 bi" :class="icon"></i>
        
        <slot></slot>

        <button v-if="dismissible" type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
    </div>
</template>

<script>

export default {
    props: {
        variant: {
            type: String,
            default: "info"
        },
        dismissible: {
            type : Boolean,
            default: false
        },
        icon: {
            type: String,
            default: ""
        }
    },

    methods: {
        /**
         * Ajout une liste de class
         * 
         * @return {String}
         */
        classList() {
            let classStringList="";

            if(this.variant) {
                classStringList += ' alert-'+ this.variant;
            }

            if(this.dismissible) {
                classStringList += ' alert-dismissible'
            }

            return classStringList;
        }
    },
}

</script>
<template>
    <div class="text-center py-4" :class="classListSpinnerBox()">
        <div :class="classListSpinner()" style="width: 3rem; height: 3rem;" role="status">
            <span class="visually-hidden">Loading...</span>
        </div>
        
        <div class="fs-4" >{{message}}</div>
    </div>
</template>

<script>
export default {
    props: {
        type: {
            type: String,
            default: "border"
        },
        variant: {
            type: String,
            default: "secondary"
        },
        message: {
            type: String,
            default: "Chargement en cours..."
        }
    },

    methods: {
        /**
         * retourn une liste de class pour l'element du spinner
         * 
         * type {String} :  - border    spinner en forme de border cercle qui turne
         *                  - grow      spinner cercle plein qui grossit
         * 
         * @return {String}
         */
        classListSpinner() {
            let classList = "";

            if(this.type) {
                classList += " spinner-" + this.type;
            }

            return classList;
        },

        /**
         * retourn une liste de class pour le message en dessous du spinner.
         * 
         * variant {String} :   - primary
         *                      - secondary (default)
         *                      - success
         *                      - danger
         *                      - warning
         *                      - info
         *                      - light
         *                      - dark
         * 
         * @return {String}
         */
        classListSpinnerBox() {
            let classList = "";
            
            if(this.variant) {
                classList += "text-" + this.variant;
            }

            return classList;
        }
    },
}
</script>
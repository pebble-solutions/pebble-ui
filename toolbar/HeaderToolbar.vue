<template>
    <div class="bg-light border-bottom px-2 py-1 shadow-sm sticky-top pebble-header-toolbar" id="app-header-toolbar"
        ref="headerToolbar">
        <slot></slot>
    </div>
</template>
  
<script>
export default {
    methods: {
        /**
         * Gère les proportions pour l'affichage de la header toolbar
         */
        resize() {
            let header = this.$refs.headerToolbar;
            let appHeader = document.getElementById('app-header');
            if (appHeader) {
                let top = appHeader.offsetHeight;


                let parentElement = header.parentElement;
                while (parentElement) {
                    if (parentElement.classList.contains('overflow-auto')) {
                        top = 0;
                        break;
                    }
                    parentElement = parentElement.parentElement;
                }

                header.style.top = top + 'px';
            }
        },
    },

    mounted() {
        this.resize();

        window.addEventListener('resize', () => {
            this.resize();
        });
    },
};
</script>
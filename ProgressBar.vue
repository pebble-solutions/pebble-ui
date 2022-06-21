<template>
    <div class="progress my-2">
        <div class="progress-bar" :class="classList('start')" role="progressbar" :style="'width:'+progressValue+'%;'" :aria-valuenow="progressValue" aria-valuemin="0" aria-valuemax="100"></div>
        <div class="progress-bar" :class="classList('end')" role="progressbar" :style="'width:'+restValue+'%;'" :aria-valuenow="restValue" aria-valuemin="0" aria-valuemax="100" v-if="twoColor"></div>
    </div>
</template>

<script>
export default {
    props: {
        twoColor: {
            type: Boolean,
            default: false
        },
        progressValue : Number,
        progressColor: String,
        restColor: String
    },

    computed: {
        restValue() {
            console.log(100-this.progressValue);
            return 100-this.progressValue;
        }
    },

    methods: {
        classList(option) {
            let classList = '';

            if(this.progressColor && option == 'start') {
                classList += ' bg-'+this.progressColor;
            }

            if(this.restColor && this.twoColor && option == 'end') {
                classList += ' bg-'+this.restColor;
            }

            return classList;
        }
    },
}
</script>
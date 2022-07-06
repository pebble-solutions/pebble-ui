<template>
    <span class="user-image" :style="'background-image:url('+imageUrl+');'" v-if="imageUrl" :class="classList"></span>
    <span class="user-image" :style="'background-color:'+name.toColor()+';'" v-else :class="classList">
        {{name[0].toUpperCase()}}
    </span>
</template>

<style lang="scss" scoped>
.user-image {
    background-size:cover;
    background-position:center; 
    border-radius:50%;
    text-decoration: none;
    color:white;
    width:32px; height:32px;
    line-height: 32px; 
    min-width: 32px; min-height: 32px;
    display: block;
    text-align: center;
    margin: auto;
}

.user-image-xs {
    width:12px; height:12px;
    line-height: 12px; 
    min-width: 12px; min-height: 12px;
}
.user-image-sm {
    width:24px; height:24px;
    line-height: 24px; 
    min-width: 24px; min-height: 24px;
}
.user-image-lg {
    width:48px; height:48px;
    line-height: 48px; 
    min-width: 48px; min-height: 48px;
}

.user-image-xl {
    width:128px; height:128px;
    line-height: 128px; 
    min-width: 128px; min-height: 128px;
}
</style>

<script>

import stc from 'string-to-color'

String.prototype.toColor = function() {
    return stc(this);
};

export default {
    props: {
        size: String,
        className: String,
        name: String,
        imageUrl: String
    },

    computed: {
        classList() {
            return this.size+' '+this.className;
        }
    }
}
</script>
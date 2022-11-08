<template>
    <div class="d-flex align-items-center justify-content-center shadow-sm my-3 mx-3 rounded limitWidth" :class="className">
        <slot></slot>
    </div>
</template>

<script>
export default {
    props: {
        className: {
            type: String,
            default: "bg-light"
        }
    }
}
</script>

<style lang="scss" scoped>
.limitWidth {   
    position: fixed;
    bottom: 0px;
    z-index: 500;
    box-sizing: border-box;
    right: 0px;
    left:0px;
}
@media (min-width: 576px) {
    .limitWidth {
        left: 52px;
    }
}
@media (min-width :1024px) {
    .limitWidth {
        left: 402px;
    }
}
</style>
    
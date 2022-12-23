<template>
    <div class="bg">
        <div
            v-for="i in floors"
            class="floor"
            :style="{ 'height': `calc(100% / ${floors})` }"
        />
    </div>
</template>

<script>
    export default {
        props: {
            floors: {
                type    : Number,
                required: false,
                default : 5,
            },
        },

        name: 'Background',
    }
</script>

<style lang="scss" scoped>
    .bg {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        z-index: 1;

    }

    .floor {
        width: 100%;
        box-sizing: border-box;
        border-top: 1px solid rgba(100, 100, 100, .1);
    }
</style>

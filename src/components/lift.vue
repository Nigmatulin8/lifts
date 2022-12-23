<template>
    <div
        class="lift"
        :style="{ 'height': `calc(100% / ${floors})` }"
    />
</template>

<script>
    export default {
        name: 'Lift',

        props: {
            lift: {
                type    : Object,
                required: true,
            },

            floors: {
                type: Number,
                required: true,
            },
        },

        data: () => ({ }),
    }
</script>

<style lang="scss" scoped>
    .lift {
        width: 100%;
        bottom: 0;
        position: absolute;
        background-color: #1e78b7;
    }
</style>
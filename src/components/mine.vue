<template>
    <div class="mine">
        <Lift 
            :lift="lift"
            :floors="floors"       
            @startLiftRest="handleStartLiftRest($event)"
            @endLiftRest="handleEndtLiftRest($event)"
            @startLift="handleStartLift($event)"
        />
    </div>
</template>

<script>
    import Lift from '../components/lift.vue';

    export default {
        name: 'Mine',

        props: {
            lift: {
                type    : Object,
                required: true,
            },

            floors: {
                type    : Number,
                required: true,
            },
        },

        components: {
            Lift,
        },

        methods: {
            handleStartLiftRest(e) {
                this.$emit('startLiftRest', e)
            },

            handleEndtLiftRest(e) {
                this.$emit('endLiftRest', e)
            },

            handleStartLift(e) {
                this.$emit('startLift', e);
            },
        },
    }
</script>

<style lang="scss" scoped>
    .mine {
        position: relative;
        width: 100px;
        height: 100%;
        border-right: 1px solid rgba(100, 100, 100, .2);
        border-left: 1px solid rgba(100, 100, 100, .2);
        margin: 0 4px;
        z-index: 2;
    }
</style>

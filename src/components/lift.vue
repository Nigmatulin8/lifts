<template>
    <div
        class="lift"
        :style="liftStyles"
    >
        {{  lift.targetFloor }}
    </div>
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

        computed: {
            liftStyles() {
                // Берём модуль разности, чтоб секунды не были отрицательным на случай, если едем сверху вниз.
                let animationSeconds = Math.abs(this.lift.targetFloor - this.lift.startFloor);

                return {
                    'height': `calc(100% / ${this.floors})`,
                    'transition': `all ${animationSeconds}s linear`,
                    'bottom': `calc((100% / ${this.floors}) * ${this.lift.targetFloor - 1})`
                }
            },
        }
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
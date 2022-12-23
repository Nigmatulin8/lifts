<template>
    <div
        class="lift"
        :class="{ 'restingLift': lift.status === -1 }"
        :style="liftStyles"
    >
        <div
            v-if="start || lift.status === -1"
            class="lift__number"
        >
            <span v-if="direction === 'up'"> &#8593; </span>
            <span v-if="direction === 'down'"> &#8595; </span>

            {{  lift.targetFloor }}
        </div>
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
                type    : Number,
                required: true,
            },
        },

        data: () => ({
            start: false,
        }),

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

            direction() {
                if (this.lift.targetFloor > this.lift.startFloor)
                    return 'up';

                if (this.lift.targetFloor < this.lift.startFloor)
                    return 'down';
            }
        },

        mounted() {
            this.$el.addEventListener('transitionstart', this.transitionStartHandler);
            this.$el.addEventListener('transitionend', this.transitionEndHandler);
        },

        beforeDestroy() {
            this.$el.removeEventListener('transitionstart', this.transitionStartHandler);
            this.$el.removeEventListener('transitionend', this.transitionEndHandler);
        },

        methods: {
            transitionStartHandler() {
                this.start = true;

                this.$emit('startLift', this.lift);
            },

            transitionEndHandler() {
                this.start = false;

                this.$emit('startLiftRest', this.lift);

                // Запускаем таймер отдыха
                setTimeout(() => {
                    this.$emit('endLiftRest', this.lift);
                }, 3000);
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

        &__number {
            font-family: 'Noto Sans';
            width: 44px;
            margin: 12px auto 0;
            padding: 4px 0;
            color: white;
            font-weight: 400;
            font-size: 16px;
            border: 1px solid white;
            border-radius: 16px;
            text-align: center;
        }
    }

    .restingLift {
        -webkit-animation: pulsate 1.2s linear infinite;
        animation: pulsate 1.2s linear infinite;
    }

    @keyframes pulsate{
        50% {
            opacity: 0.5;
        }
    }
</style>

<template>
    <div
        class="button"
        :style="{ 'height': `calc(100% / ${floors})` }"
    >
        <button
            class="button__item"
            :class="{ 'button__item-highlight': highlightButton }"
            :disabled="highlightButton"
            @click="callLift(buttonFloor)"
        >
            {{ buttonFloor }}
        </button>
    </div>
</template>

<script>
    export default {
        name: 'Button',

        data: () => ({
            buttonFloor: 0,
        }),

        props: {
            // Стек вызовов
            floorCallStack: {
                type    : Array,
                required: false,
                default : [],
            },

            // Все этажи
            floors: {
                type    : Number,
                required: true,
            },

            // Этаж, к которой принажлежит кнопка
            floor: {
                type    : Number,
                required: true,
            },
        },

        computed: {
            highlightButton() {
                return this.floorCallStack.indexOf(this.buttonFloor) !== -1;
            },
        },

        mounted() {
            this.buttonFloor = this.floors - this.floor + 1;
        },

        methods: {
            callLift(floor) {
                this.$emit('click', floor);
            },
        },
    }
</script>

<style lang="scss">
    .button {
        display: flex;
        align-items: center;
        justify-content: center;

        &__item {
            width: 28px;
            height: 28px;
            font-size: 16px;
            text-align: center;
            font-weight: 600;
            font-family: 'Roboto';
            color: #1e78b7;
            background: none;
            border-radius: 50%;
            border: none;
            outline: 2px solid #1e78b7;
            cursor: pointer;

            &-highlight {
                outline: 2px solid rgba(229,57,53,.7);
                color: rgba(229,57,53,.7);
                cursor: default;
            }
        }
    }
</style>

<template>
    <div
        class="button"
        :style="{ 'height': `calc(100% / ${floors})` }"
    >
        <button @click="callLift(floors - floor + 1)">
            {{ floors - floor + 1  }}
        </button>
    </div>
</template>

<script>
    export default {
        name: 'Button',

        props: {
            // Все этажи
            floors: {
                type: Number,
                required: true,
            },

            // Этаж, к которой принажлежит кнопка
            floor: {
                type: Number,
                required: true,
            },
        },

        methods: {
            callLift(floor) {
                this.$emit('click', floor);
            },
        }
    }
</script>

<style lang="scss">
    .button {
        display: flex;
        align-items: center;
        justify-content: center;

        button {
            width: 28px;
            height: 28px;
            font-family: 'Roboto';
            background: none;
            border-radius: 50%;
            border: none;
            outline: 2px solid #1e78b7;
            cursor: pointer;
        }
    }
</style>

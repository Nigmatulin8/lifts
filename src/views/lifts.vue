<template>
    <div class="lifts">
        <Background
            :floors="floors"
        />

        <Mine
            v-for="lift in lifts"
            :lift="lift"
            :floors="floors"
            :key="lift.id"
        />
    </div>
</template>

<script>
    import Background from '../components/bg.vue';
    import Mine from '../components/mine.vue';

    export default {
        name: 'Lifts',

        components: {
            Background,
            Mine,
        },

        data: () => ({
            floors: 7,
            lifts: [
                /**
                 * @param id - id лифта -_-
                 * @param status - -1 - отдыхает, 0 - свободен, 1 - едет
                 * @param targetFloor - Этаж на который едем
                 * @param currentFloor - Этаж с которого начали движение. Если targetFloor === currentFloor, то мы на месте
                 * @param rested - Отдохнул лифт или нет. Ставим false сразу после запуска и переключаем обратно через 3с после остановки
                 */
                {
                    id          : 0,
                    status      : 0,
                    targetFloor : 0,
                    currentFloor: 0,
                    rested      : true,
                },
            ],
        }),
    }
</script>

<style lang="scss">
    .lifts {
        position: relative;
        display: flex;
        width: 100vw;
        height: 100vh;
    }
</style>
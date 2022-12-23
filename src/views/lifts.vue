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

        <div class="buttons">
            <div
                class="buttons__wrapper"
                :style="{ 'height': `calc(100% / ${floors})` }"
                v-for="floor in floors"
            >
                <button @click="callLift(floors - floor + 1)">
                   {{ floors - floor + 1  }}
                </button>
            </div>
        </div>
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
            floors: 5,
            lifts: [
                /**
                 * @param id - id лифта -_-
                 * @param status - -1 - отдыхает, 0 - свободен, 1 - едет
                 * @param targetFloor - Этаж на который едем
                 * @param startFloor - Этаж с которого стартанули
                 */
                {
                    id          : 0,
                    status      : 0,
                    targetFloor : 1,
                    startFloor  : 0,
                },
            ],
        }),

        methods: {
            callLift(floor) {
                // Наш прошлый целевой этаж также является нашим этажом старта
                this.lifts[0].startFloor = this.lifts[0].targetFloor;

                this.lifts[0].targetFloor = floor;
            }
        }
    }
</script>

<style lang="scss">
    .lifts {
        position: relative;
        display: flex;
        width: 100vw;
        height: 100vh;
    }

    .buttons {
        display: flex;
        flex-direction: column;
        width: 100px;
        height: 100%;
        z-index: 2;
        margin-left: 8px;
        border-right: 1px solid rgba(50, 100, 150, .2);
        border-left: 1px solid rgba(50, 100, 150, .2);

        &__wrapper {
            display: flex;
            align-items: center;
            justify-content: center;
        }
    }
</style>
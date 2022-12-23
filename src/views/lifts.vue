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
            @startLift="handleStartLift($event)"
            @startLiftRest="handleStartLiftRest($event)"
            @endLiftRest="handleEndLiftRest($event)"
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
                    startFloor  : 1,
                },
                {
                    id          : 1,
                    status      : 0,
                    targetFloor : 1,
                    startFloor  : 1,
                },
                {
                    id          : 2,
                    status      : 0,
                    targetFloor : 1,
                    startFloor  : 1,
                },
                {
                    id          : 3,
                    status      : 0,
                    targetFloor : 1,
                    startFloor  : 1,
                },
            ],
        }),

        methods: {
            getLiftPosition(activeLift) {
                // Находим индекс активного лифта в общем списке лифтов
                return this.lifts.map(lift => lift.id).indexOf(activeLift.id);
            },

            callLift(floor) {
                // Наш прошлый целевой этаж также является нашим этажом старта
                // this.lifts[0].startFloor = this.lifts[0].targetFloor;

                // this.lifts[0].targetFloor = floor;

                for (let i = 0; i < this.lifts.length; i++) {
                    if (this.lifts[i].status === 0) {
                        this.lifts[i].startFloor = this.lifts[i].targetFloor;
                        this.lifts[i].targetFloor = floor;
                        break;
                    }
                }
            },

            handleStartLift(e) {
                const pos = this.getLiftPosition(e);

                this.lifts[pos].status = 1;
            },

            handleStartLiftRest(e) {
                const pos = this.getLiftPosition(e);

                this.lifts[pos].status = -1;
            },

            handleEndLiftRest(e) {
                const pos = this.getLiftPosition(e);

                this.lifts[pos].status = 0;
            },
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
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
            <Button
                v-for="i in floors"
                :floorCallStack="floorCallStack"
                :floors="floors"
                :floor="i"
                :key="i"
                @click="callLift"           
            />
        </div>
    </div>
</template>

<script>
    import Mine from '../components/mine.vue';
    import Background from '../components/bg.vue';
    import Button from '../components/button.vue';

    export default {
        name: 'Lifts',

        components: {
            Mine,
            Button,
            Background,
        },

        data: () => ({
            floors: 7, // Количество этажей | Кол-во этажей менять тут
            lifts: [
                /**
                 * Кол-во лифтов менять тут
                 * 
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

            floorCallStack: [], // Стек вызовов лифта
        }),

        watch: {
            floorCallStack: {
                handler() {
                    this.liftsRun();
                },
            },
        },

        mounted() {
            this.getFromLocaleStorage();

            this.liftsRun();
        },

        beforeDestroy() {
            this.saveToLocaleStorage();
        },

        methods: {
            getLiftActiveLiftData(activeLift) {
                // Находим индекс активного лифта в общем списке лифтов
                return {
                    activeLift,
                    pos: this.lifts.map(lift => lift.id).indexOf(activeLift.id),
                };
            },

            isLiftOnTheFloor(floor) {
                // Проверяем есть ли уже лифт на этаже, на которых хотим его вызвать
                return this.lifts.map(lift => lift.targetFloor).indexOf(floor);
            },

            callLift(floor) {
                // Если в очереди нету этажа и на этом этаже уже нету лифта,
                // и на него ещё никто не едет, то можем к нему отправить свободный лифт
                if (this.floorCallStack.indexOf(floor) === -1 && this.isLiftOnTheFloor(floor) === -1)
                    this.floorCallStack.push(floor);
            },

            handleStartLift(e) {
                // Лифт поехал
                const data = this.getLiftActiveLiftData(e);
                const pos = data.pos;
                this.lifts[pos].status = 1;
            },

            handleStartLiftRest(e) {
                // Лифт приехал и начал отдыхать
                const data = this.getLiftActiveLiftData(e);
                const pos = data.pos;

                this.lifts[pos].status = -1;

                this.saveToLocaleStorage();
            },

            handleEndLiftRest(e) {
                // Лифт закончил отдыхать и готов ехать
                const data = this.getLiftActiveLiftData(e);
                const pos = data.pos;

                this.lifts[pos].status = 0;

                // Удаляем этаж, на который уже выехали (удаляем после отдыха лифта)
                this.floorCallStack = this.floorCallStack.filter(floor => floor !== data.activeLift.targetFloor);

                this.saveToLocaleStorage();
            },

            liftsRun() {
                /**
                 * 1. Смотрим, едут ли на целевой этаж лифты или уже находятся там, если да, то берем следующий этаж и тд.
                 * 2. Если на выбранный этаж никто не едет, то ищем ближайший лифт к целевому и запускаем.
                 */
                for (let i = 0; i < this.floorCallStack.length; i++) {
                    // Занят этаж или нет. По умолчанию этаж свободный.
                    let isFloorIsBusy = false;

                    for (let j = 0; j < this.lifts.length; j++) {
                        // Если на целевой этаж уже кто-то едет, то заканчиваем упражнение (лифт должен быть свободным)
                        if (this.floorCallStack[i] === this.lifts[j].targetFloor) {
                            isFloorIsBusy = true;
                            break;
                        }

                        if (j === this.lifts.length - 1 && !isFloorIsBusy) {
                            // Собираем все свободные лифты
                            let freeLifts = this.lifts.filter(lift => lift.status === 0);

                            // Если нету ближайшего сразу, то первый попавшийся лифт будет самым близким
                            let closestLift = freeLifts.length ? freeLifts[0] : null;

                            // Ищем ближайший лифт к целевому этажу
                            if (freeLifts.length) {
                                for (let k = 0; k < freeLifts.length; k++) {
                                    // Разница от ближайшего лифта до целевого этажа
                                    const diffBetweenClosestLift = closestLift.targetFloor - this.floorCallStack[i];
                                    const diffBerweenPotentialLift = freeLifts[k].targetFloor - this.floorCallStack[i];

                                    if (Math.abs(diffBetweenClosestLift) > Math.abs(diffBerweenPotentialLift))
                                        closestLift = freeLifts[k];  
                                }
                            }

                            // Если у нас есть подходящий лифт, то запускаем его.
                            // Если нет, значить все занято. Ждем когда освободится
                            if (closestLift !== null) {
                                const readyLiftIndex = this.lifts.map(l => l.id).indexOf(closestLift.id);

                                this.lifts[readyLiftIndex].startFloor = this.lifts[readyLiftIndex].targetFloor;
                                this.lifts[readyLiftIndex].targetFloor = this.floorCallStack[i];
                            }

                            return;
                        }
                    }
                }
            },

            saveToLocaleStorage() {
                // Убираем промежуточные состояния у лифтов
                const liftsListForSave = this.lifts.map(lift => lift = {
                    ...lift,
                    status: 0,
                });

                // Сохраняем в стор
                localStorage.setItem('lifts', JSON.stringify(liftsListForSave));
                localStorage.setItem('stack', JSON.stringify(this.floorCallStack));
            },

            getFromLocaleStorage() {
                // Достаём из стора
                const lifts = localStorage.getItem('lifts');
                const stack = localStorage.getItem('stack');

                if (lifts && stack) {
                    const parsedLifts = JSON.parse(lifts);
                    let parsedStack = JSON.parse(stack);

                    // Если мы сохранили положения лифтов в стеке в каком-то промежуточном состоянии,
                    // то при получении данных из хранилища мы будем использовать завершенное состояние лифтов на момент сохранения.
                    const allTargetFloors = parsedLifts.map(lift => lift.targetFloor);

                    if (parsedStack.length)
                        parsedStack = parsedStack.filter((pos) => !allTargetFloors.includes(pos));

                    this.lifts = parsedLifts;
                    this.floorCallStack = parsedStack;
                }
            },
        },
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
    }
</style>

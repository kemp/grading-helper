<template>
    <div class="flex flex-col">
        <h1 class="text-4xl mb-16">Grading Helper <button @click="changeIcon()" v-text="icon"></button></h1>

        <div class="mb-4 flex flex-col">
            <label for="total" title="The total number of points the student can earn on this assignment">Points possible:</label>
            <div class="flex w-full">
                <input
                    class="border rounded p-4 flex-grow dark:text-gray-800 dark:bg-gray-200 disabled:dark:bg-gray-400 disabled:cursor-not-allowed min-w-0 max-w-full"
                    id="total"
                    type="text"
                    inputmode="decimal"
                    autocomplete="off"
                    min="0"
                    ref="total"
                    :class="{ 'invalid': (errorMsg !== '') }"
                    :disabled="locked"
                    :readonly="locked"
                    v-model.number="total"
                    @blur="validateTotal()"
                    @focus="errorMsg = ''"
                >

                <button
                    class="p-2 rounded ml-2"
                    :title="'Press to ' + (locked ? 'unlock' : 'lock')"
                    v-text="!locked ? '🔒' : '✏️'"
                    @click="toggleLock()"
                ></button>
            </div>
            <p class="text-sm text-red-600" v-text="errorMsg"></p>
        </div>


        <div class="mb-4 flex flex-col">
            <label for="points_missed" title="The number of points the student missed on the assignment.">Points missed:</label>
            <input
                class="border rounded p-4 dark:text-gray-800 dark:bg-gray-200 min-w-0 max-w-full"
                id="points_missed"
                min="0"
                type="text"
                inputmode="decimal"
                autocomplete="off"
                v-model.number="points_missed"
                :max="total"
            >
        </div>

        <div class="mt-16">
            <div v-if="isNaN(score) || !isFinite(score)">
                <h2 class="font-bold">Enter values above to calculate</h2>
            </div>
            <div v-else-if="score < 0 || score > 100">
                <h2 class="font-bold text-red-800">Score is not between zero and one hundred</h2>
            </div>
            <div v-else>
                <h2 class="font-bold">
                    Final Score:
                    <span class="pl-4 circled" v-text="score.toFixed(0) + '%'" :title="score"></span>
                </h2>
            </div>
        </div>
    </div>
</template>

<script lang="ts">

const ICONS = ['🍎', '📝', '🍏', '👨‍🎓','🧑‍🎓','👩‍🎓','👩‍💻','☕', '🎱', '🧘', '😤', '😭', '🤣', '👩‍🏫', '🎒', '📚', '💯'];

export default {
    name: 'HelloWorld',
    data() {
        return {
            total: null,
            points_missed: null,
            locked: false,
            icon: '',
            errorMsg: '',
        }
    },
    computed: {
        score() {
            return (this.total - this.points_missed) / this.total * 100;
        }
    },
    watch: {
        locked(newLocked) {
            if (newLocked) {
                localStorage.total = this.total;
            } else {
                localStorage.removeItem('total');
            }

            localStorage.locked = newLocked ? 1 : 0;
        },
    },
    mounted() {
        if (localStorage.total) {
            this.total = localStorage.total;

            // Only lock if we have a total
            if ('locked' in localStorage) {
                this.locked = localStorage.locked && localStorage.locked !== 'false';
            }
        }

        this.randomlyChangeIcon();
    },
    methods: {
        changeIcon() {
            this.icon = ICONS[Math.floor(Math.random() * ICONS.length)];
        },
        randomlyChangeIcon() {
            this.changeIcon();

            setTimeout(this.randomlyChangeIcon, Math.random() * 30000);
        },
        toggleLock() {
            this.validateTotal();

            if (this.errorMsg === '' || this.locked) {
                this.locked = ! this.locked;
            }
        },
        validateTotal() {
            let errorMsg = '';

            if (isNaN(this.total) || !isFinite(this.total)) {
                errorMsg = "Please enter a valid number";
            } else if (this.total < 0) {
                errorMsg = "Please enter a number greater than zero";
            } else if (this.total === '' || this.total === null) {
                errorMsg = "Please enter a number";
            }

            this.$refs.total.setCustomValidity(errorMsg);
            this.errorMsg = errorMsg;
        }
    },
}
</script>

<style scoped>

#total.invalid {
    @apply bg-red-200;
}

.circled {
    background: url('/circled.png') center center;
    background-size: contain;
    background-repeat: no-repeat;
    padding: 35px 30px 25px;
}

</style>
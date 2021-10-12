<template>
    <div class="hello">
        <h1>Grading Helper</h1>
        <div style="margin-bottom: 2rem;">
            <label for="total">Points possible:</label><br>
            <input id="total" v-model.number="total" type="number">
        </div>


        <div style="margin-bottom: 2rem;">
            <label for="points_missed">Points missed:</label><br>
            <input id="points_missed" v-model.number="points_missed" type="number">
        </div>

        <div v-if="! isNaN(score)">
            <h2>Final score: <span v-text="score"></span>%</h2>
        </div>
        <div v-else>
            <h2>Enter values above to calculate</h2>
        </div>
    </div>
</template>

<script lang="ts">
export default {
    name: 'HelloWorld',
    data() {
        return {
            total: 0,
            points_missed: 0,
        }
    },
    computed: {
        score() {
            const score = (this.total - this.points_missed) / this.total * 100;

            return score.toFixed(0);
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
    margin: 40px 0 0;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    display: inline-block;
    margin: 0 10px;
}

a {
    color: #42b983;
}
</style>

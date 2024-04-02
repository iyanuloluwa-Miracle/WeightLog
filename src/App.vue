<script setup>
import {ref, shallowRef, computed,watch, nextTick} from 'vue'
import Chart from 'chart.js/auto'

const weights = ref([])

const weightChartEl = ref(null)
const weightChart = shallowRef(null)

const weightInput = ref(60.0)

const currentWeight = computed(() =>{
    return weights.value.sort((a,b) => b.date - a.date)[0] || { weight: 0}
})

const addWeight = () =>{
    weights.value.push({
        weight: weightInput.value,
        date: new Date().getTime()
    })

}

watch(weights, newWeights =>{
    const ws = [...newWeights]
    nextTick(()=>{
        weightChart.value= new Chart(weightChartEl.value.getContext('2d'),{
            type:'line',
            data:{
                labels:ws.sort((a,b)=> a.date - b.date).map(w => new Date(w.date).toLocaleDateString())
            }
        })
    })
}, {deep: true})


</script>

<template>
    <main>
        <h1>Weight<strong>Log</strong></h1>
        <div>
            <span>{{ currentWeight.weight }}</span>
            <small>Current Weight (kg)</small>
        </div>

        <form @submit.prevent="addWeight">
            <input type="number" step="0.1" v-model="weightInput"/>

            <input type="submit" value="Add weight"/>

        </form>

        <div v-if="weights && weights.length > 0">
            <h2>Last 7 days</h2>

            <div class="canvas-box">
                <canvas ref="weightChartEl"></canvas>
            </div>
            
            <div class="weight-history">
                <h2>Weight History</h2>
                <ul>
                    <li v-for="weight in weights" :key="weight.date">
                        <span>{{ weight.weight }}</span>
                        <small>{{ new Date(weight.date).toLocaleDateString() }}</small>

                    </li>
                   
                </ul>
            </div>

        </div>

    </main>

  
</template>

<style scoped>
  @import url("https://fonts.googleapis.com/css2?family=Ojuju:wght@200..800&display=swap");
  
  * {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: "Ojuju", sans-serif;
  }

</style>

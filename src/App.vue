<script setup>
import { ref, shallowRef, computed, watch, nextTick } from 'vue'
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

    if(weightChart.value){
    weightChart.value.data.labels = ws
            .sort((a,b)=> a.date - b.date)
            .slice(-7)
            .map(w => new Date(w.date).toLocaleDateString());

    weightChart.value.data.datasets[0].data = ws
            .sort((a,b)=> a.date - b.date)
            .slice(-7)
            .map(w => w.weight);
    weightChart.value.update();

    return;
}



    nextTick(()=>{
        weightChart.value= new Chart(weightChartEl.value.getContext('2d'),{
            type:'line',
            data:{
                labels:ws
                .sort((a,b)=> a.date - b.date)
                .slice(-7)
                .map(w => new Date(w.date).toLocaleDateString()),
                datasets:[
                {
                    label:'Weight',
                    data:ws
                    .sort((a,b)=> a.date - b.date)
                    .slice(-7)
                    .map(w => w.weight),
                    backgroundColor:'#b5ea8c',
                    borderColor:'#94bf73',          
                    borderWidth:1,
                    fill: true

                }
            ]
            },
            options:{
                responsive:true,
                maintainAspectRatio: false
            }

        })
    })
}, {deep: true})
</script>

<template>
  <main>
    <h1>Weight<strong>Log</strong></h1>
    <div class="current">
      <span>{{ currentWeight.weight }}</span>
      <small>Current Weight (kg)</small>
    </div>

    <form @submit.prevent="addWeight">
      <input type="number" step="0.1" v-model="weightInput" />

      <input type="submit" value="Add weight" />
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
            <span>{{ weight.weight }}kg</span>
            <small> {{ new Date(weight.date).toLocaleDateString() }}</small>
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

body{
  background-color: #edf2fa;
}
main{
  padding:1.5rem;
}

h1{
  font-size: 3em;
  color: #72b043;
  text-align: center;
  margin-bottom: 2rem;

}

h2{
  margin-bottom: 1rem;
  color: #503d5c;
  font-weight: 400;
}

.current{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  width: 200px;
  height: 200px;

  text-align: center;
  background-color: white;
  border-radius: 999px;
  box-shadow: 0 4px 12px rgba(0,0,0, 0.1);
  border: 5px solid #94bf73 ;

  margin:0 auto 2rem;

}


.current span{
  display: block;
  font-size: 2em;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

.current small{
  color: #11150d;
  font-weight: bold;
}

</style>

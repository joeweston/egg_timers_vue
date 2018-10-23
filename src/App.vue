<template>
  <div id="app">
    <h2>
      <span>Egg Timers</span>
    </h2>
    <div id="timers-holder">
        <egg-timer-component v-for="n in timersCount" :timerNumber=n :key=n></egg-timer-component>
   </div>
    <div>
      <button @click="addTimer">Add Timer</button>
      <button @click="removeTimer" :disabled="timersCount===1">Remove Timer</button>
    </div>
  </div>
</template>

<script>
  import EggTimerComponent from "./components/EggTimerComponent";


  export default {
    components:{
      EggTimerComponent
    },
    data () {
      return  {
        timersCount: 1
      };
    },
    computed:{

    },
    methods: {
      addTimer: function(){
        this.timersCount++;
      },
      removeTimer: function(){
        this.timersCount--;
      }
    }

  }
</script>

<style lang="scss" scoped>
#app{
}
#timers-holder{
  display: flex;
  flex-wrap: wrap;
  border: 1px solid black;
}

</style>

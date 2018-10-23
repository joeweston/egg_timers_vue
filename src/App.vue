<template>
  <div id="app">
    <div id="heading-section">
      <h2>
        <span>Egg Timers</span>
      </h2>
      <div>
        <button @click="addTimer">Add Timer</button>
        <button @click="removeTimer" :disabled="timersCount===1">Remove Timer</button>
      </div>
    </div>
    <div id="timers-holder">
        <egg-timer-component v-for="n in timersCount" :timerNumber=n :key=n></egg-timer-component>
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

<style lang="scss">
@import "assets/reset.css";
$main-font: Arial, Helvetica, sans-serif;
*, *:before, *:after {
  box-sizing: border-box;
}
#app{
  font-family: $main-font;
}
h2{
  font-size: 20px;
  margin: 0 10px;
}
#heading-section{
  margin-top: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  button{
    height: 60px;
    width: 60px;
    margin: 5px;
    border: none;
    border-radius: 20px;
    transition: background-color .4s;
    box-shadow:  0 0 8px 0 rgb(60,60,255);
    &:hover:enabled{
      background-color: #f7fbe2;
    }
    &:active{
      box-shadow:  0 0 2px 0 grey,
             inset 1px 1px 2px 0 grey;
    }
    &:focus{
      outline: none;
    }
    &:disabled{
      color: #ddd;
      box-shadow:  0 0 8px 0 rgb(255,60,60),
    }
  }
}
#timers-holder{
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

</style>

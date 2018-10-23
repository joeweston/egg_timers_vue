 <template>
  <div class="timer" style="height:242px;width:164px;">
    <h3>Timer {{ timerNumber }}</h3>
    <div :style=eggCss class="egg" style="height:192px;width:144px;">
      <div class="time-container">
        <button class="minute-plus" v-if="canStartingTimeChange" @click="incrementMinute">+</button>
        <button class="minute-minus" v-if="canStartingTimeDecrement" @click="decrementMinute">-</button>
        <button class="second-plus" v-if="canStartingTimeChange" @click="incrementSecond">+</button>
        <button class="second-minus" v-if="canStartingTimeDecrement" @click="decrementSecond">-</button>
        <div v-if="state==='off'" class="time">{{ paddedStartingMinutes }} : {{ paddedStartingSeconds }}</div>
        <div v-if="state!=='off'" class="time">{{ paddedMinutes }} : {{ paddedSeconds }}</div>
      </div>
      <div class="button-container">
        <button :disabled="state==='on'" @click="start">Start</button>
        <button :disabled="state!=='on'" @click="pause">Pause</button>
        <button :disabled="state==='off'" @click="stop">Stop/Reset</button>
      </div>
    </div>
  </div>
</template>

<script>
const audio = new Audio(require("../assets/audio/alarm_clock.ogg"));
const [ DEFAULT_MINUTES, DEFAULT_SECONDS ] = [ 1, 0 ];
const TICK_STATE = { 
  ON: "on",
  OFF: "off",
  PAUSED: 'paused'
}

export default {
  props: [ "timerNumber" ],
  data(){
    return {
      startingMinutes: DEFAULT_MINUTES,
      startingSeconds: DEFAULT_SECONDS,
      minutes: DEFAULT_MINUTES,
      seconds: DEFAULT_SECONDS,
      state: TICK_STATE.OFF,
      finished: false
    }
  },
  computed:{
    paddedMinutes: function(){
      return this.pad(this.minutes);
    },
    paddedSeconds: function(){
      return this.pad(this.seconds);
    },
    paddedStartingMinutes: function(){
      return this.pad(this.startingMinutes);
    },
    paddedStartingSeconds: function(){
      return this.pad(this.startingSeconds);
    },
    canStartingTimeChange: function(){
      return this.state === TICK_STATE.OFF; //can't change when on
    },
    canStartingTimeDecrement: function(){
      return this.canStartingTimeChange &&
       !(this.startingMinutes === 0 && this.startingSeconds <= 1);
    },
    eggCss: function(){
      return this.getTimeGraphicCss();
    }
  },
  methods:{
    start: function(){
      this.finished = false;
      this.state = TICK_STATE.ON;
      this.tick(); //click immeatately commences first click
      this.interval = setInterval(this.tick, 1000);
    },
    pause: function(){
      clearInterval(this.interval);
      this.state = TICK_STATE.PAUSED;
    },
    stop: function(){
      clearInterval(this.interval);
      this.state = TICK_STATE.OFF;
      this.sincStartingtime();
    },
    tick: function(){
      if (this.seconds === 1 && this.minutes === 0){
        audio.pause();
        audio.currentTime = 0;
        audio.play();
      }
      if (this.seconds !== 0){
        this.seconds--;
      } else if (this.minutes !== 0){ //&& seconds === 0
        this.minutes--;
        this.seconds = 59;
      } else { //both minutes and seconds are zero - so stop
        this.stop();
        this.finished = true;
      }
    },
    incrementMinute: function(){
      this.startingMinutes++
      this.sincStartingtime();
      this.finished = false;
    },
    decrementMinute: function(){
      if ( this.startingMinutes <= 1){
        this.startingSeconds = 1;
        this.startingMinutes = 0;
      } else {
        this.startingMinutes--;
      }
      this.sincStartingtime();
      this.finished = false;
    },
    incrementSecond: function(){
      if ( this.startingSeconds === 59 ){
        this.startingMinutes++;
        this.startingSeconds = 0;
      } else {
        this.startingSeconds++
      }
      this.sincStartingtime();
      this.finished = false;
    },
    decrementSecond: function(){
      if ( this.startingSeconds === 0 ){
        this.startingMinutes--;
        this.startingSeconds = 59;
      } else {
        this.startingSeconds--
      }
      this.sincStartingtime();
      this.finished = false;
    },
    getTimeGraphicCss: function(){
      let totalSeconds = this.minutes * 60 + this.seconds;
      if( totalSeconds === 0 || this.finished){
        return "background-image: radial-gradient(red 100%, orange)";
      }
      let totalStartingSeconds = this.startingMinutes * 60 + this.startingSeconds
      let pixels = 100 - ((totalSeconds/totalStartingSeconds) * 100); 
      let css =  `background-image: radial-gradient(red, orange, #feffed ${pixels}%)`;
      return css;
    },
    //Helpers
    pad: function(number){
      return number < 10 ? "0" + number: number;
    },
    sincStartingtime: function(){
      this.minutes = this.startingMinutes;
      this.seconds = this.startingSeconds;
    }
  },
  beforeDestroy(){
    clearInterval(this.interval);
  },



}


</script>

<style lang="scss" scoped>
.timer{
  padding: 10px;
  overflow: hidden;
}
h3{
 text-align: center;
 margin: 5px 0 10px 0;
}
.egg{
  border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  box-shadow:  5px 5px 10px 0px #eee;
}

.time-container{
  display: grid;
  grid-template-rows: 20px 20px 20px;
  grid-template-columns: 20px 20px 20px;
  grid-template-areas:
    "mp .  sp"
    " t  t  t"
    "mm .  sm";
}
.time-container > *{
  display: flex;
  justify-content: center;
  align-items: center;
}
button{
  border: none;
  box-shadow:  0 0 2px 0 grey;
  transition: background-color .2s;
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
}
.minute-plus{
  grid-area: mp;
  border-radius: 50% 50% 0 0;
}
.minute-minus{
  grid-area: mm;
  border-radius: 0 0 50% 50%;
}
.second-plus{
  grid-area: sp;
  border-radius: 50% 50% 0 0;
}
.second-minus{
  grid-area: sm;
  border-radius: 0 0 50% 50%;
}
.time{
  grid-area: t;
  line-height: 15px;
}

.button-container{
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  margin-top: 20px;
  button{
    padding: 5px;
    border-radius: 4px;
    &:last-child{
      margin-top: 10px;
    }
    &:disabled{
      color: #ddd;
      box-shadow:  0 0 2px 0 grey,
             inset 1px 1px 2px 0 grey;
    }
  }
}


</style>











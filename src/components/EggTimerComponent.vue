 <template>
  <div class="timer">





    <h3>Timer {{ timerNumber }}</h3>
    <div>
      <h4>Default Time</h4>
      <button :disabled="!canStartingTimeChange" @click="incrementMinute">+</button>
      <button :disabled="!canStartingTimeDecrement" @click="decrementMinute">-</button>
      <button :disabled="!canStartingTimeChange" @click="incrementSecond">+</button>
      <button :disabled="!canStartingTimeDecrement" @click="decrementSecond">-</button>
      <p>{{ paddedStartingMinutes }} : {{ paddedStartingSeconds }}</p>
    </div>
    <div>
      <h4>Time</h4>
      <p>{{ paddedMinutes }} : {{ paddedSeconds }}</p>
    </div>
    <div>
      <button :disabled="state==='on'" @click="start">Start</button>
      <button :disabled="state!=='on'" @click="pause">Pause</button>
      <button :disabled="state==='off'" @click="stop">Stop/Reset</button>
    </div>
    <div :style=eggCss class="image" style="height:80px;width:60px"></div>
  </div>
</template>

<script>
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
      state: TICK_STATE.OFF
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
       !(this.startingMinutes === 0 && this.startingSeconds === 0);
    },
    eggCss: function(){
      return this.getTimeGraphicCss();
    }
  },
  methods:{
    start: function(){
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
      this.minutes = this.startingMinutes;
      this.seconds = this.startingSeconds;
    },
    tick: function(){
      if (this.seconds !== 0){
        this.seconds--;
      } else if (this.minutes !== 0){ //&& seconds === 0
        this.minutes--;
        this.seconds = 59;
      } else { //both minutes and seconds are zero - so stpo
        this.stop();       
      }
    },
    incrementMinute: function(){
      this.startingMinutes++
      this.sincStartingtime();
    },
    decrementMinute: function(){
      if ( this.startingMinutes < 1){
        this.startingSeconds = 0;
        this.startingMinutes = 0;
      } else {
        this.startingMinutes--;
      }
      this.sincStartingtime();
    },
    incrementSecond: function(){
      if ( this.startingSeconds === 59 ){
        this.startingMinutes++;
        this.startingSeconds = 0;
      } else {
        this.startingSeconds++
      }
      this.sincStartingtime();
    },
    decrementSecond: function(){
      if ( this.startingSeconds === 0 ){
        this.startingMinutes--;
        this.startingSeconds = 59;
      } else {
        this.startingSeconds--
      }
      this.sincStartingtime();
    },
    getTimeGraphicCss: function(){
      let totalSeconds = this.minutes * 60 + this.seconds;
      if( totalSeconds === 0){
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
  }


}


</script>

<style lang="scss" scoped>
.timer{
  width: 400px;
  border: 1px solid black;
  overflow: hidden;
}
.image{
  content: "";
  border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
}

</style>
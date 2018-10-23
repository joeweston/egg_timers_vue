 <template>
  <div>
    <span>{{ paddedMinutes }} : {{ paddedSeconds }}</span>
  </div>
</template>

<script>
export default {
  props: ["minutes","seconds"],
  computed:{
    paddedMinutes: function(){
      return this.minutes < 10 ? "0" + this.minutes: this.minutes;
    },
    paddedSeconds: function(){
      return this.seconds < 10 ? "0" + this.seconds: this.seconds;
    }
  }

}


</script>

<style lang="scss">

</style>
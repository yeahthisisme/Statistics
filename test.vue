<template>
  <div>
    <a-button type="primary"> Primary </a-button>
    <a-button>Default</a-button>
    <a-button type="dashed"> Dashed </a-button>
    <a-button type="danger"> Danger </a-button>
    <a-config-provider :auto-insert-space-in-button="false">
      <a-button type="primary"> 按钮 </a-button>
    </a-config-provider>
    <a-button type="primary"> 按钮 </a-button>
    <a-button type="link"> {{t}} </a-button>
    <a-time-picker  v-model="date1" format="HH:mm" @change="dateChange"/>
<!-- 
    <a-time-picker
      v-model="date"
      :default-value="'23:08'"
      value-format="HH:mm"
      format="HH:mm"
      @change="dateChange"
    /> -->
</template>

<script>
module.exports = {
  data() {
    return {
      t:1,
      date1: moment('13:18', 'HH:mm'),
    }
  },
  computed:{
    date(value){
      console.log(value)
      return moment(this.date1).format("HH:mm");
    }
  },

  mounted(){
    // this.date = new Date()
  },
  methods: {
    moment,
    dateChange(e){
      // console.log(e)
      // console.log(this.date)
    }
  },
}
</script>

<style></style>

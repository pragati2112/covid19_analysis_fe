<template>
  <div id="main" style="width:1000px;height:400px">

  </div>
</template>

<script>

import axios from "axios";

export default {
  name: 'HelloWorld',
  data(){
    return{
        x_axis: [],
        y_axis: []
    }
  },

  mounted(){
    this.axiosInstance = axios.create({
      timeout:50000,
      headers:{
        'Content-Type': 'application/json',
      }
    });

    this.fetchConfirmedCases();
    this.drawChart();
  },


  methods:{
    fetchConfirmedCases(){
      this.axiosInstance.get("/cases/confirmed")
              .then(response=>{
                 var str=response.data;
                 if (str.includes("\"")) {
                    var valid_str = JSON.parse(str);
                 }
                 console.log(valid_str) ;
                 this.$data.x_axis = valid_str.json_xax;
                 this.$data.y_axis = valid_str.json_yax;

              }).catch(error=> {
                  console.log(error.response.data);
      });

    },
      drawChart(){
          // eslint-disable-next-line no-undef
          var myChart = echarts.init(document.getElementById('main'));
          myChart.setOption({
              title: {
                  text: 'ECharts entry example'
              },
              tooltip: {},
              xAxis: {
                  data: ['shirt', 'cardign', 'chiffon shirt', 'pants', 'heels', 'socks']
              },
              yAxis: {},
              series: [{
                  name: 'sales',
                  type: 'bar',
                  data: [5, 20, 36, 10, 10, 20]
              }]
          });
      },

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

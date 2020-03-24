<template>
  <div id="main" style="width:100%;height:400px">

  </div>
</template>

<script>

import axios from "axios";

export default {
  name: 'HelloWorld',
  data(){
    return{

    }
  },

  mounted(){
    this.axiosInstance = axios.create({
      timeout:50000,
      headers:{
        'Content-Type': 'application/json',
      }
    });

    this.drawChart();
  },


  methods:{
      drawChart(){
          // eslint-disable-next-line no-undef
          var myChart = echarts.init(document.getElementById('main'));
        this.axiosInstance.get("/cases/confirmed")
                .then(response=>{
                  var str=response.data;
                  if (str.includes("\"")) {
                    var valid_str = JSON.parse(str);
                  }

                   var arr_x = valid_str.json_xax;
                   var arr_y = valid_str.json_yax;

                   //timestamp is in nanoseconds
                   ////convert timestamp to human readable form
                    arr_x.map(function(arr){
                    var arr_humanDate = (new Date(arr/1000000)).toLocaleDateString();
                    console.log(arr_humanDate);

                     myChart.setOption({
                       title: {
                         text: 'Confirmed Cases:'
                       },
                       tooltip: {},
                       xAxis: {
                         name: 'Date',
                         data: arr_x
                       },
                       yAxis: {
                         name: 'No. of patients'
                       },
                       series: [{
                         name: 'No. of patients',
                         type: 'line',
                         data: arr_y
                       }]
                     });
                   });

                }).catch(error=> {
                     console.log(error.response.data);
                   });

      },

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

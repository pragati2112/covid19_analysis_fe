<template>
  <div>
      <div>
        <b-navbar class="menu-wrapper">
          <template slot="start" >
            <b-navbar-item href="">
              COVID-19
            </b-navbar-item>
          </template>
          <template slot="end">
            <b-navbar-item href="">
              Model Prediction
            </b-navbar-item>
            <b-navbar-item href="">
              About Us
            </b-navbar-item>
            <b-navbar-item href="">
              Sources
            </b-navbar-item>
            <b-navbar-item href="">
              Contribute
            </b-navbar-item>
            <b-navbar-item href="">
              <button class="button" style="background-color: #80B5FF">Contact us</button>
            </b-navbar-item>

          </template>

        </b-navbar>
      </div>


      <section class="hero is-large" style="background-color: #042554">
        <div class="hero-body">
          <div class="container">
            <h1 class="title is-2">
              Everything you wanted to know about <span style="color:#FF6663;">COVID-19</span>
            </h1>
            <button class="button has-text-primary" style="background-color: #042554" @click="scrollWin()"> Know More</button>
          </div>
        </div>
      </section>

    <section class="content">

        <div class="container">
          <h1 class="title is-3">
            CoronaVirus outbreak in World
          </h1>

         <div class="confirmedCasesChart">
           <div id="main" style="width:100%; height:300px; padding-top:1rem">

           </div>
         </div>

            <div class="columns" style="margin-top: 10px">
                <div class="column totalCases">
                    <p class="title is-4">
                        Affected People
                    </p>
                    <p class="subtitle is-2">
                        {{confirmedCases.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, "$1,")}}
                    </p>
                </div>
                <div class="column totalCases">
                    <p class="title is-4">
                        People Cured
                    </p>
                    <p class="subtitle is-2" >
                        {{recoveredCases.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, "$1,")}}
                    </p>
                </div>
                <div class="column totalCases">
                    <p class="title is-4">
                        People Died
                    </p>
                    <p class="subtitle is-2">
                        {{deathCases.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, "$1,")}}
                    </p>
                </div>
            </div>

             <div class="columns" style="padding: 1rem 0rem 0rem 0rem">
               <div class="column mortalCases">
                 <div  style="height:400px; width:100% ;">
                 </div>
               </div>

                 <div class="column mortalCases" style="margin-left: 1rem">
                     <div id="bar" style="height:400px; width:100% ;">
                     </div>
                 </div>
             </div>

     </div>
    </section>


  <section class="secondContent">
      <div class="container">
          <h1 class="title is-3">
              Regional Corona Virus Outbreak
          </h1>
      </div>
  </section>

  </div>
</template>

<script>

import axios from "axios";

export default {
  name: 'HelloWorld',
  data(){
    return{
      confirmedCases: '',
      recoveredCases: '',
      deathCases: '',
      totalCountriesAffected: ''
    }
  },

  mounted(){
    this.axiosInstance = axios.create({
      timeout:50000,
      headers:{
        'Content-Type': 'application/json',
      }
    });
    this.totalCasesWorld();
    this.drawChart();
    this.drawMortalityBarGraph();
  },


  methods:{
    scrollWin(){
      window.scrollBy(0,600);
    },
      totalCasesWorld(){
        this.axiosInstance.get("/cases/total")
        .then(response=> {
          var str= response.data;
          if (str.includes("\"")) {
            var valid_str = JSON.parse(str);
          }
          this.$data.confirmedCases = valid_str.confirmed;
          this.$data.recoveredCases = valid_str.recovered;
          this.$data.deathCases = valid_str.deaths;
          this.$data.totalCountriesAffected = valid_str.totalCountries;

        }).catch(error=>{
          console.log(error.response.data)
        })
      },

      drawChart(){
          // eslint-disable-next-line no-undef
          var myChart = echarts.init(document.getElementById('main'));
          this.axiosInstance.get("/cases/confirmed")
                .then(response=>{
                  console.log(response.data)
                  var str=response.data;
                  if (str.includes("\"")) {
                    var valid_str = JSON.parse(str);
                  }

                   var arr_x = valid_str.json_xax;
                   var arr_y = valid_str.json_yax;

                   //timestamp is in nanoseconds
                   ////convert timestamp to human readable form
                    var array_humanDate=[]
                    arr_x.forEach(function(arr){
                    var arr_humanDate = (new Date(arr/1000000)).toLocaleDateString();
                    array_humanDate.push(arr_humanDate)

                     myChart.setOption({
                       title: {
                         left: 'left',
                         text: 'Rise in total number of Cases:'
                       },

                       tooltip: {
                         trigger: 'axis',
                         position: function (pt) {
                           return [pt[0], '10%'];
                         }
                       },
                       xAxis: {
                         name: 'Date',
                         data: array_humanDate
                       },
                       yAxis: {
                         name: 'No. of patients'
                       },
                       dataZoom: [{
                         type: 'inside',
                         start: 0,
                         end: 10
                       }, {
                         start: 0,
                         end: 10,
                         handleIcon: 'M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z',
                         handleSize: '80%',
                         handleStyle: {
                           color: '#fff',
                           shadowBlur: 3,
                           shadowColor: 'rgba(0, 0, 0, 0.6)',
                           shadowOffsetX: 2,
                           shadowOffsetY: 2
                         }
                       }],
                       series: [{
                         name: 'No. of patients',
                         type: 'line',
                         smooth: true,
                         symbol: 'none',
                         sampling: 'average',
                         itemStyle: {
                           color: 'rgb(255, 70, 131)'
                         },
                         areaStyle: {
                           // eslint-disable-next-line no-undef
                           color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
                             offset: 0,
                             color: 'rgb(255, 158, 68)'
                           }, {
                             offset: 1,
                             color: 'rgb(255, 70, 131)'
                           }])
                         },
                         data: arr_y
                       }]
                     });
                   });

                }).catch(error=> {
                     console.log(error.response.data);
                   });

      },


    drawMortalityBarGraph(){
      // eslint-disable-next-line no-undef
      var myChart = echarts.init(document.getElementById('bar'));
      this.axiosInstance.get("/perCountry/mortality")
      .then(response=>{
        console.log(response.data)
        var str=response.data;
        if (str.includes("\"")) {
          var valid_str = JSON.parse(str);
        }

        var arr_x = valid_str.json_xax;
        var arr_y = valid_str.json_yax;
        console.log(arr_y,arr_x)

        myChart.setOption(
                 {
                     title: {
                         text: 'Mortality in Countries',
                         subtext: '(World-wide)'
                     },
                     tooltip: {
                         trigger: 'axis',
                         axisPointer: {
                             type: 'shadow'
                         }
                     },
                     legend: {
                         data: ['Mortality']
                     },
                     grid: {
                         left: '3%',
                         right: '4%',
                         bottom: '3%',
                         containLabel: true
                     },
                     xAxis: {
                         type: 'value',
                         boundaryGap: [0, 0.01]
                     },
                     yAxis: {
                         type: 'category',
                         data: arr_y
                     },
                     series: [
                         {
                             name: 'Mortality Rate',
                             type: 'bar',
                             data: arr_x,
                             color:'#0071BB',
                         },

                     ]
                }

      )
      }).catch(error=>{
        console.log(error.response.data)
      })

    },

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.title.is-3{
  color:white;
  padding-top: 2rem;
  padding-bottom: 3rem;
}
  .content{
    width:100%;
    height:80rem;
    background-color: #01132c;
    margin-bottom: auto!important;
  }
.confirmedCasesChart{
  width:100%;
  height:20rem;
  background-color: white;
  padding:0rem 3rem 0rem 3rem;
  border-radius: 15px;
}
  .title.is-4{
    color: #01132c;
    padding-bottom: 1rem;
    padding-top: 1rem;
  }

.mortalCases{
  height:28rem;
  background-color: white;
  padding: 1rem 1rem 1rem 1rem;
  border-radius: 15px;
}
.secondContent{
    padding: 0rem;
    width:100%;
    height:40rem;
    background-color: #042554;
}

</style>

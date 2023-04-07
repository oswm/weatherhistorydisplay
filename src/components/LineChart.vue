<template>
    <div class="LineChart">
      <div :id="id" style="width: 800px;height:600px;"></div>
    </div>
</template>
  
<script>
import * as echarts from 'echarts'

  export default {
    name: 'LineChart',
    props: {
        id: String,
        title: String,
        data: Array,
        data2: Array,
    },
    data() {
        return {
            month: [...Array(12).keys()],
        }
    },
    methods:{
        myLineChart(){
            // 基于准备好的dom，初始化echarts实例
            var myChart = echarts.init(document.getElementById(this.id));
  
            // 指定图表的配置项和数据
            var option = {
                title: {
                    text: this.title,
                },
                tooltip: {},
                legend: {
                    data:['气温']
                },
                xAxis: {
                    data: this.month,
                    axisLabel: {
                        formatter: '{value} °C'
                    }
                },
                yAxis: {
                    type: 'value',
                },
                series: [{
                    name: '气温',
                    type: 'line',
                    smooth: true,
                    data: this.data,
                },
            ]
            };
  
            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
            }
    },
    mounted() {
        this.month = this.month.map(x => x = x+1);
        console.log(this.month);
        console.log(this.data);
        this.myLineChart();
    }
  }
</script>
  
<style>
  
</style>
  
  
<template>
    <div class="DoubleLineChart">
      <div :id="id" style="width: 800px;height:600px;"></div>
    </div>
</template>
  
<script>
import * as echarts from 'echarts'

  export default {
    name: 'DoubleLineChart',
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
                    data:['最高气温', '最低气温' ],
                },
                xAxis: {
                    data: this.month,
                    
                },
                yAxis: {
                    type: 'value',
                    axisLabel: {
                        formatter: '{value} °C'
                    }
                },
                series: [{
                    name: '最高气温',
                    type: 'line',
                    smooth: true,
                    data: this.data,
                }, {
                    name: '最低气温',
                    type: 'line',
                    smooth: true,
                    data: this.data2,
                }
            ]
            };
  
            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
            }
    },
    mounted() {
        console.log(this.title);
        this.month = this.month.map(x => x = x+1);
        this.myLineChart();
    }
  }
</script>
  
<style>
  
</style>
  
  
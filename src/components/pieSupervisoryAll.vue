<template>

  <div class=" w-full  flex  relative m-1">
    <div class="panel w-full absolute"></div>
    <div class="w-[calc(100%-1rem)] h-[calc(100%-2rem)]  absolute m-2 flex flex-col">
      <div class="w-full h-12  flex flex item-center">
        <div class="text-xl  w-56 text-white  items-center flex justify-center">各监控异常类型汇总</div>

      </div>
      <div class="w-full h-[calc(100%-3rem)] flex">
        <div class=" w-[60%] h-full justify-center items-center">
          <div ref="chartDiv" :style="{ width: '100%', height: '100%' }"></div>
        </div>
        <div class=" w-[40%] flex items-center justify-center p-2">
          <div class="data  h-[100%] w-[80%] ">
            <div class=" w-full h-[50%] items-center justify-center flex flex-col gap-1">
              <div class="w-32 text-2xl text-white justify-start items-center flex">
                {{AppGlobal.dataSupervisoryTotality}}
              </div>
              <span class="flex  items-center w-32 padding-2 ">
                <i class="icon-dot text-lg bg-[rgb(237,63,52)] opacity-100 rounded w-3 mr-2 "
                   style="color: rgb(237,63,52)"></i>
                <div class="text-xl text-[rgb(237,63,52)]">异常总数</div>
                        </span>
            </div>
            <div class=" w-full h-[50%] items-center justify-center flex flex-col gap-1">
              <div class="w-32 text-2xl text-white justify-start items-center flex">
                {{AppGlobal.dataSupervisoryStatusDay.statusSupervisoryAdd}}
              </div>
              <span class="flex  items-center w-32 padding-2 ">
                <i class="icon-dot text-lg bg-[#006cff] opacity-100 rounded w-3 mr-2 " style="color: #006cff"></i>
                <div class="text-xl text-[#006cff]">本日新增</div>
                        </span>
            </div>
          </div>

        </div>

      </div>


    </div>

  </div>
</template>

<script lang="ts" setup>
import { defineProps, onMounted, onUnmounted, ref, watch } from 'vue'
import { ECharts, EChartsOption, init } from 'echarts'

import { useAppGlobal } from '@/store/AppGlobal'

const chartDiv = ref<HTMLElement | null>(null)
let chartEch: ECharts | null = null


const AppGlobal = useAppGlobal();
const updateChart = () => {

  const option: EChartsOption = {
    tooltip: {
      trigger: 'item',
      formatter: '{a} <br/>{b} : {c} ({d}%)'
    },
    legend: {
      textStyle: {
        color: '#fff'
      },
      left: 'center',
      top: 'bottom',
      data: AppGlobal.kindSupervisoryStatistics.statusSet
    },
    toolbox: {
      show: true,
      feature: {
        mark: { show: true },
        dataView: {
          show: true,
          readOnly: false
        },
        restore: { show: true },
        saveAsImage: { show: true }
      }
    },
    series: {
      name: '异常类型',
      type: 'pie',
      // 南丁格尔玫瑰图 有两个圆  内圆半径10%  外圆半径70%
      // 百分比基于  图表DOM容器的半径
      radius: ['10%', '70%'],
      // 图表中心位置 left 50%  top 50% 距离图表DOM容器
      center: ['50%', '50%'],
      // 半径模式，另外一种是 area 面积模式
      roseType: 'radius',
      itemStyle: {
        borderRadius: 5
      },
      // 将kindSupervisoryStatistics的状态值导出为data
      data: AppGlobal.kindSupervisoryStatistics.statusPieSupervisory,
      //文字调整
      label: {
        fontSize: 20,
        color: '#fff'
      },
      //引导线
      labelLine: {
        length: 8,
        length2: 10
      }
    }

  }
  if (chartEch !== null) {

    chartEch.setOption(option)
    chartEch.resize()
  }
}

watch(() => AppGlobal.kindSupervisoryStatistics.statusPieSupervisory, () => {
  updateChart()
})
onMounted(() => {
  setTimeout(() => {
    if (chartDiv.value) {
      chartEch = init(chartDiv.value)
      window.addEventListener('resize', () => {
        if (chartEch) {
          chartEch.resize()
        }
      })

      updateChart()
    }
  }, 500) // 延迟 500 毫秒
})

onUnmounted(() => {
  if (chartEch) {
    chartEch.dispose()
    chartEch = null
  }
  window.removeEventListener('resize', updateChart)
})

</script>


<style scoped>
.panel {
  /* 边框 */
  box-sizing: border-box;
  border-image: url(@/assets/image/border.png) 51 38 21 132;
  border-width: 2.125rem 1.583rem 0.875rem 5.5rem;
}

.icon-dot:before {
  content: "\e900";
}

.data {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  box-sizing: border-box;
  background-image: url(@/assets/image/rect.png);
  background-size: cover;
}
</style>

<template>
  <div id="mycheckbox">
    <p v-for="(item, index) in items">
      <input
        type="checkbox"
        id="'checked' + index"
        @input="onClick($event, item.prefCode, item.prefName)"
      />
      <label for="'checked' + index">{{ item.prefName }} </label>
    </p>
  </div>
  <!-- {{ chartdata }} -->
  <LineChart ref="chart" :chart-data="chartdata" />
</template>

<script setup>
//js
import axios from "axios";
import { ref, onMounted, defineComponent } from "vue";
import { Chart, registerables } from "chart.js";
import { LineChart } from "vue-chart-3";


const url = "https://opendata.resas-portal.go.jp/api/v1/prefectures";
const url2 ="https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?cityCode=-&prefCode=";
const headers = {
  "X-Api-Key": "y3y97v7GaGN3GQLKCKxaaAXeKNxmLVZb76UWyV1O",
};

const getData = async () => {
  let { data } = await axios.get(url, { headers });
  // console.log(data);
  items.value = data.result;
};

const items = ref([]);

const onClick = async (event, prefCode, prefName) => {
  if (event.target.checked == true) {
    // console.log(test)
    // let {data}  = await axios.get(url, {headers});
    const URL_population = url2 + prefCode;
    await axios.get(URL_population, { headers }).then((res) => {
      const pref = res.data.result.data[0].data; //総人口のデータのみ取得
      const props = {
        labels: {
          type: Array,
          requires: true,
        },
        datasets: {
          type: Object,
          requires: true,
        },
      };

      const { labels } = ref(props);

      const year = [];
      const value = [];
      const namelabel = prefName;

      //それぞれに格納している
      pref.forEach((element) => {
        year.push(element.year);
      });

      pref.forEach((element) => {
        if (value.includes(element.value) == false) {
          value.push(element.value);
        }
      });

      const pref_data = {
        label: namelabel,
        data: value,
      };

      all_data.value.push(pref_data);

      const data3 = {
        labels: year,
        datasets: all_data.value,
      };
      chartdata.value = data3;
    });
  } else {
    const index = all_data.value.findIndex((el) => el.label === prefName);
    all_data.value.splice(index, 1);
    console.log(chart.value)
    chart.value.chartInstance.data.datasets.splice(index, 1);
  }
};

const all_data = ref([]);
const year = ref([]);
const chart = ref();
console.log(chart.value)

onMounted(async () => {
  await getData();
  console.log(chart.value)
});

const name = defineComponent("myChart");
const components = {
  Chart,
};
const chartdata = ref({});
const direction = ref("horizontal");
</script>

<script>
Chart.register(...registerables);
</script>

<style scoped>
#mycheckbox {
  display: flex;
  flex-wrap: wrap;
}

#mycheckbox div {
  width: 25%;
}
</style>

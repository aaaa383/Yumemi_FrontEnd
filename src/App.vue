<template>
  <div>
    <li v-for="(item, index) in items">
      <input
        type="checkbox"
        id="'checked' + index"
        @input="onClick($event, item.prefCode)"
      />
      <label for="'checked' + index"
        >{{ index }}-{{ item.prefCode }}-{{ item.prefName }}-{{
          "checked" + index
        }}</label
      >
    </li>
  </div>
  <Chart

    :size="{ width: 600, height: 400 }"
    :data="chartdata"
    :margin="margin"
    :direction="direction"


  >
    <template #layers>
      <Grid strokeDasharray="2,2" />
      <Line :dataKeys="['year', 'value']" />
    </template>
  </Chart>
</template>

<script setup>
//js
import axios from "axios";
import { ref, onMounted, defineComponent } from "vue";
import { Chart, Grid, Line } from "vue3-charts";

const url = "https://opendata.resas-portal.go.jp/api/v1/prefectures";
const url2 =
  "https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?cityCode=-&prefCode=";
const headers = {
  "X-Api-Key": "y3y97v7GaGN3GQLKCKxaaAXeKNxmLVZb76UWyV1O",
};

const getData = async () => {
  let { data } = await axios.get(url, { headers });
  // console.log(data);
  items.value = data.result;
};

const items = ref([]);

const onClick = async (event, prefCode) => {
  if (event.target.checked) {
    // console.log(test)
    // let {data}  = await axios.get(url, {headers});
    const URL_population = url2 + prefCode;
    await axios.get(URL_population, { headers }).then((res) => {
      const pref = res.data.result.data[0].data; //総人口のデータのみ取得

      const tmp_data = {"label": String(prefCode),
                  "data": []
      }

      pref.forEach(element => {
          tmp_data.data.push(element.value);
        });

      datasets.value.push(tmp_data)
      console.log(datasets.value)
      console.log("chartdata:", chartdata.value)
    });
  }
};

// const labels = ref([])
// const data =ref([])
// const datasets = ref([
//   {data: [1,2,3]},
//   {labels: [1,2,3]}
// ])

const datasets = ref([]);


const items2 = ref([[]]);
onMounted(async () => {
  await getData();
});

const name = defineComponent("myChart");
const components = {
  Chart,
  Grid,
  Line,
};
const chartdata = ref(datasets);
const direction = ref("horizontal");
const margin = ref({
  left: 0,
  top: 20,
  right: 20,
  bottom: 0,
});
</script>



<style scoped>
/* CSS */
</style>
9
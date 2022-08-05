<template>
  <h1>
    <font size="5">フロント開発課題</font>
  </h1>
  <div id="mycheckbox">
    <p v-for="(item, index) in items" :key="`item${index}`">
      <input
        type="checkbox"
        :id="'checked' + index"
        @input="onClick($event, item.prefCode, item.prefName)"
      />
      <label :for="'checked' + index">{{ item.prefName }} </label>
    </p>
  </div>
  <LineChart ref="chart" :chart-data="chartdata" :options="option" />
</template>

<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";
import { Chart, registerables } from "chart.js";
import { LineChart } from "vue-chart-3";
import { computed } from "@vue/reactivity";

const headers = {
  "X-Api-Key": import.meta.env.VITE_API_KEY,
};

const getData = async () => {
  let { data } = await axios.get(import.meta.env.VITE_URL + "prefectures", {
    headers,
  });
  items.value = data.result;
};

const items = ref([]);
const ColorList = [
  "rgba(255,0,0,0.8)",
  "rgba(255, 99, 132, 0.5)",
  "rgba(255,215,0)",
  "rgb(255,165,0,1)",
  "rgb(0,0,255,0.8)",
  "rgb(0,191,255)",
  "rgb(50,205,50)",
  "rgba(153, 102, 255, 0.5)",
  "rgb(160,82,45,0.8)",
];
const onClick = async (event, prefCode, prefName) => {
  if (event.target.checked == true) {
    await axios
      .get(
        import.meta.env.VITE_URL +
          "population/composition/perYear?cityCode=-&prefCode=" +
          prefCode,
        { headers }
      )
      .then((res) => {
        const pref = res.data.result.data[0].data;

        const year = [];
        const value = [];
        const namelabel = prefName;

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
          borderColor: ColorList[(prefCode - 1) % 9],
        };

        all_data.value.push(pref_data);

        const feature_data = {
          labels: year,
          datasets: all_data.value,
        };
        chartdata.value = feature_data;
      });
  } else {
    const index = all_data.value.findIndex((el) => el.label === prefName);
    all_data.value.splice(index, 1);
    chart.value.chartInstance.data.datasets.splice(index, 1);
  }
};

const all_data = ref([]);
const chart = ref();

const option = computed(() => {
  return {
    plugins: {
      title: {
        display: true,
        text: "都道府県別 人口データ",
        align: "center",
        font: { size: 14 },
      },
    },
    scales: {
      y: {
        title: {
          display: true,
          text: "総人口数",
          font: {
            size: 20,
          },
        },
      },
      x: {
        min: 1960,
        title: {
          display: true,
          text: "年",
          font: {
            size: 20,
          },
        },
      },
    },
  };
});

onMounted(async () => {
  await getData();
});

const chartdata = ref({});

Chart.register(...registerables);
</script>

<style scoped>
h1 {
  padding: 0.4em;
  line-height: 20px;
  margin: 0.1em;
  text-align: left;
  color: #494949;
  background: #fef5ec;
  border-left: solid 5px #ffaf58;
}

#mycheckbox {
  display: flex;
  flex-wrap: wrap;
  border: solid;
  border-color: #fed9b1;
  border-radius: 8px;
}

#mycheckbox div {
  width: 25%;
}
</style>

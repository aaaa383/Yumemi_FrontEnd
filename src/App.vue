<template>
  <div>
    <li v-for="(item, index) in items">
      <input
        type="checkbox"
        id="checked"
        v-model="check_box"
        @input="onClick($event, item.prefCode)"
      />
      <label for="checked"
        >{{ index }}-{{ item.prefCode }}-{{ item.prefName }}</label
      >
    </li>
  </div>
</template>

<script setup>
//js
import axios from "axios";
import { ref, reactive, onMounted } from "vue";

const url = "https://opendata.resas-portal.go.jp/api/v1/prefectures";
const url2 =
  "https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?cityCode=11362&prefCode=";
const headers = {
  "X-Api-Key": "y3y97v7GaGN3GQLKCKxaaAXeKNxmLVZb76UWyV1O",
};

const getData = async () => {
  console.log("aaa");
  let { data } = await axios.get(url, { headers });
  console.log(data);
  items.value = data.result;
};

const items = ref([]);

const onClick = async (event, prefCode) => {
  if (event.target.checked) {
    // console.log(test)
    // let {data}  = await axios.get(url, {headers});
    const URL_population = url2 + prefCode;
    let data2 = await axios.get(URL_population, { headers });
    console.log(data2);
    console.log();
  }
};
//クリックしたときにそれぞれの都道府県のPrefCodeを取る。

onMounted(async () => {
  await getData();
  // onClick();
});
</script>

<style scoped>
/* CSS */
</style>

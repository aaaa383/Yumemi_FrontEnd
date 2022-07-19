
<template>
  <section class="alert alert-primary">
    <textarea v-model="data.mydata"></textarea>
  </section>

</template>

<script setup>
//js
import axios from 'axios';
import { reactive, onMounted } from 'vue';

const url = "https://opendata.resas-portal.go.jp/api/v1/prefectures"
const headers = {
  'X-Api-Key':'y3y97v7GaGN3GQLKCKxaaAXeKNxmLVZb76UWyV1O'
}
const data = reactive({
  mydata: "",
});
const getData = async () =>{
  console.log('aaa')
  let result = await axios.get(url, {headers});
  console.log(result)
  data.mydata = result.data;
  };

onMounted(() => {
  getData();

});
</script>



<style scoped>
/* CSS */
</style>

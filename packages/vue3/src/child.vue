<template>
  <section>
    <h3>模拟请求</h3>
    <button @click="request">请求</button>
    <button @click="abort">中止</button>
    <button @click="cancel">取消</button>
    <button @click="ready1 = !ready1">ready1: {{ ready1 }}</button>
    <Loading v-if="isLoading" />
    <h2>data => <span v-if="data">{{ data }}</span></h2>
    <h2>params => <span v-if="params">{{ params }}</span></h2>
    <h3>error => <span v-if="error">{{ error }}</span></h3>
    <h3>isFinished: 已完成<em>{{ isFinished }}</em></h3>
    <h3>isAborted: 中止<em>{{ isAborted }}</em></h3>
    <h3>isLoading: 加载中<em>{{ isLoading }}</em></h3>

  </section>
</template>
<script setup lang="ts">
// import { useRequest } from "@async-handler/request/vue3-request";
import axios from "axios";
import { reactive, ref, watch, watchEffect } from "vue";
import { useRequest } from "vue3-request";

// import { useRequest } from "vue-request";

// axios
const axiosInstance = axios.create({
  // ...
});

axiosInstance.interceptors.response.use((response) => response.data); // 响应拦截器，自己业务项目想怎么配置都可以

// 模拟请求示例
const testService = (params: { age: number }, signal?: AbortSignal): Promise<{
  code: number;
  msg: string;
  data: number;
  request_id: string;
}> => {
  console.log('params', params)
  return axiosInstance.get('https://v2.xxapi.cn/api/renjian', {
    signal
  })
};
const ready1 = ref(false)
const throttleOptions = reactive({
  leading: true,
})
const { data, params, error, isLoading, isFinished, isAborted, run, abort, cancel, runAsync } = useRequest((signal) => (params: { age: number }) => testService(params, signal), {
  throttleWait: 2000,
  manual: true,
  onSuccess: (data, params) => {
    console.log('onSuccess->child', data, params)
  },
  onError: (error, params) => {
    console.log('onError->child', error, params)
  },
}
)

// const { data: data2, error: error2, } = useRequest(() => testService({ age: 17 }), {
//   manual: true,
//   onSuccess: (data, params) => {
//     console.log('onSuccess->child', data, params)
//   },
// })

const request = async () => {
  run({ age: 17 })
  // const res = await runAsync({ age: 17 })
}


</script>

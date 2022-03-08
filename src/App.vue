<template>
<div class="bg-slate-100 py-8">
  <div class="container my-0 mx-auto space-y-4">
    <h1 class="text-5xl">{{ initData.userName }} repo List : </h1>
    <DataList :data="dataList" />
    <div v-if="isInit && !isLoading">loading...</div>
    <div v-show="isLoading" ref="dataListEnd" />
  </div>
</div>
</template>

<script>
import { onMounted, ref } from 'vue';
import axios from 'axios';
import DataList from './components/DataList.vue';

export default {
  components: {
    DataList,
  },
  setup() {
    const initData = {
      userName: 'franklion',
      page: 1,
      perPage: 6,
    };
    const isInit = ref(false);
    const isLoading = ref(true);
    const dataList = ref([]);
    const isEnd = ref(false);

    const getDataList = async () => {
      try {
        const { data } = await axios.get(`https://api.github.com/users/${initData.userName}/repos?page=${initData.page}&per_page=${initData.perPage}`);
        if (!isInit.value) {
          dataList.value = data;
        } else {
          dataList.value.push(...data);
          if (data.length < 6) {
            isEnd.value = true;
          }
        }
      } catch (error) {
        console.error(error);
      }
      isLoading.value = true;
    };
    const dataListEnd = ref(null);
    const entriesCallback = (entries, observer) => {
      Object.values(entries).forEach((entry) => {
        if (entry.isIntersecting) {
          if (isLoading.value && !isEnd.value) {
            isLoading.value = false;
            initData.page += 1;
            getDataList();
          } else {
            observer.unobserve(dataListEnd.value);
          }
        }
      });
    };

    const init = async () => {
      await getDataList();
      const observer = new IntersectionObserver(entriesCallback, {
        threshold: 0,
      });
      observer.observe(dataListEnd.value);
      isInit.value = true;
    };
    onMounted(() => {
      init();
    });
    return {
      isEnd,
      isInit,
      dataListEnd,
      dataList,
      initData,
      isLoading,
    };
  },
};
</script>

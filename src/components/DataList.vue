<template>
  <div class="px-4 py-8 bg-white" v-for="item in data" :key="item.id">
    <h2 class="text-2xl">{{ item.name }}</h2>
    <p>{{ item.description }}</p>
    <a class="text-blue-500" :href="item.clone_url">{{ item.clone_url }}</a>
  </div>
</template>

<script>
export default {
  name: "DataList",
  props: {
    data: {
      type: Array,
      default: () => [],
    },
  },
};
</script>

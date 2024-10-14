<template>
  <div>
    <h1>Open Bar Tab</h1>
    <BeverageList />
  </div>
</template>

<script>
import BeverageList from '@/components/BeverageList.vue';

export default {
  components: {
    BeverageList,
  },
};
</script>

<style scoped>
h1 {
  text-align: center;
  margin-bottom: 20px;
}
</style>

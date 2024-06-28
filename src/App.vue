<template>
  <div id="app">
    <category-checkbox
      v-for="(item, index) in categories"
      :key="index"
      :category="item"
    />
  </div>
</template>

<script>
import categories from './categories.json';
import CategoryCheckbox from '@/components/CategoryCheckbox.vue';

export default {
  name: 'App',
  components: {
    CategoryCheckbox
  },
  data() {
    return {
      categories: categories 
    };
  }
};
</script>



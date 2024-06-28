<template>
  <div class="category-container">
    <div class="category-box">
      <div class="category-checkbox">
        <div class="category-checkbox__wrapper">
          <input
            class="category-checkbox__input"
            type="checkbox"
            :checked="isChecked"
            :indeterminate="isIndeterminate"
            @change="handleCheck"
          />
          <label class="category-checkbox__label">{{ category.title }}</label>
        </div>
        <div v-if="category.categories && category.categories.length" class="category-checkbox__nested">
          <category-checkbox
            v-for="(subCategory, index) in category.categories"
            :key="index"
            :category="subCategory"
            @update:check="handleUpdate"
          />
        </div>
      </div>
    </div>
  </div>
</template>


<script>
export default {
  name: 'CategoryCheckbox',
  props: ['category'],
  emits: ['update:check'],
  data() {
    return {
      isChecked: this.category.checked,
      isIndeterminate: false
    };
  },
  methods: {
    updateParent() {

  if (!this.$parent || typeof this.$parent.handleUpdate !== 'function') {
    console.warn("No parent with handleUpdate method found");
    return;
  }
  

  if (!this.category.categories || !Array.isArray(this.category.categories)) {
    this.isIndeterminate = false;
    this.isChecked = this.category.checked; 
    return;
  }

  const allChecked = this.category.categories.every(child => child.checked);
  const anyChecked = this.category.categories.some(child => child.checked);

  this.isIndeterminate = anyChecked && !allChecked;
  this.isChecked = allChecked;


  this.$parent.handleUpdate({ checked: this.isChecked, category: this.category });
},
    handleCheck(event) {
      const checked = event.target.checked;
      this.isChecked = checked;
      this.isIndeterminate = false; // Обнуление indeterminate 
      this.updateChildrenChecked(this.category, checked);
      this.$emit('update:check', { checked, category: this.category });
      this.updateParent();
    },
    updateChildrenChecked(category, checked) {
      category.checked = checked; // Обновление текущей категории
      if (category.categories) {
        category.categories.forEach(child => {
          this.updateChildrenChecked(child, checked); // Рекурсивное обновление
        });
      }
    },
    // eslint-disable-next-line
    handleUpdate({ checked, category }) {
      this.updateIndeterminateStatus();
      this.$emit('update:check', { checked, category: this.category });
    },
    updateIndeterminateStatus() {
      if (!this.category.categories || !this.category.categories.length) {
        return;
      }
      const total = this.category.categories.length;
      const checkedCount = this.category.categories.filter(c => c.checked).length;

      this.isIndeterminate = checkedCount > 0 && checkedCount < total;
      this.isChecked = checkedCount === total;
    }
  },
  watch: {
    'category.checked': function(newVal) {
      this.isChecked = newVal;
      this.updateIndeterminateStatus();
    }
  }
};
</script>

<style lang="scss">
$primary-color: #7abafd;
$indeterminate-color: #a1fba7;
$background-color: #ffffff; 
$shadow-color: rgba(0, 0, 0, 0.1); 
$border-radius: 3px; 

.category-container {
  display: block;
  width: 200px;
}

.category-checkbox {
  font-family: 'Arial', sans-serif;
  margin-bottom: 10px;

  &__wrapper {
    display: flex;
    align-items: center; 
  }

  &__input {
    cursor: pointer;
    width: 24px;
    height: 24px;
    appearance: none;
    border: 1px solid #ccc;
    background-color: #f9f9f9;
    transition: background-color 0.3s, border-color 0.3s;
    border-radius: $border-radius;
    position: relative;

    &:checked {
      background-color: $primary-color;
      border-color: $primary-color;
    }

    &:checked::after {
      content: '';
      display: block;
      width: 8px;
      height: 12px;
      border: solid $background-color;
      border-width: 0 2px 2px 0;
      transform: rotate(45deg) translate(-50%, -50%);
      position: absolute;
      top: 50%;
      left: 23%;
    }

    &:indeterminate {
      background-color: $indeterminate-color;
      border-color: $indeterminate-color;
    }

    &:indeterminate::before {
      content: '';
      display: block;
      width: 12px;
      height: 2px;
      background-color: $background-color;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
    }
  }

  &__label {
    cursor: pointer;
    padding-left: 5px;
  }

  &__nested {
    padding-left: 30px;
  }
}



// .pl-4 {
//   padding-left: 1rem;
// }
</style>




  
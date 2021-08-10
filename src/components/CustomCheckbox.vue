<template>
  <span class="custom-checkbox">
    <input type="checkbox" :id="'checkbox' + id" name="options[]" :value="value">
    <label :for="'checkbox' + id"></label>
  </span>
</template>

<script>
export default {
  name: 'CustomCheckbox',
  props: {
    id: String,
    value: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

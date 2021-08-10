<template>
  <a
    :href="href" 
    :class="btnType" 
    data-toggle="modal"
  >
    <i 
      class="material-icons"
      :data-toggle="!hasText? 'tooltip': null"
      :title="!hasText? text : null"
    >
      {{icon}}
    </i> 
    <span v-if="hasText">{{text}}</span>
  </a>
</template>

<script>
export default {
  name: 'CustomButton',
  props: {
    text: String,
    icon: String,
    type: String,
    href: String
  },
  computed: {
    btnType: function() {
      let value = this.type;

      if (value=="edit" || value=="delete") {
        return value;
      } else {
        return 'btn btn-' + value;
      }
    },
    hasText: function() {
      let value = this.type;

      if(value=="edit" || value=="delete") {
        return false;
      } else {
        return true;
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

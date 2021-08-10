<template>
  <tr>
    <td>
      <CustomCheckbox :id="id" :value="id" />
    </td>
    <td>{{name}}</td>
    <td>{{email}}</td>
    <td>{{address}}</td>
    <td>{{phone}}</td>
    <td>
      <CustomButton 
        icon="&#xE254;"
        type="edit"
        text="Edit"
        href="#addEmployeeModal"
        v-on:click="editEmployee"
      />
      <CustomButton 
        icon="&#xE872;"
        type="delete"
        text="Delete"
        href="#deleteEmployeeModal"
        v-on:click="setId"
      />
    </td>
  </tr>
</template>

<script>
import CustomCheckbox from "../CustomCheckbox"
import CustomButton from "../CustomButton"
export default {
  name: 'DataRow',
  components: {
    CustomCheckbox,
    CustomButton
  },
  props: {
    id: String,
    name: String,
    email: String,
    address: String,
    phone: String
  },
  methods: {
    setId: function() {
      this.$root.$data.selectedEmployee = this.id;
    },
    editEmployee: async function() {
      this.$root.$data.submitButtonText="Edit";
      this.$root.$data.submitFormTitle="Edit Employee";
      this.setId();
      let singleEmployee = await this.$root.getSingleEmployee();
      if(singleEmployee) {
        this.$root.$data.newEmployee.name = singleEmployee.name;
        this.$root.$data.newEmployee.email = singleEmployee.email;
        this.$root.$data.newEmployee.address = singleEmployee.address;
        this.$root.$data.newEmployee.phone = singleEmployee.phone;
        console.log(this.$root.$data.newEmployee);
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

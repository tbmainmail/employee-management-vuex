<template>
  <div class="container-xl">
  <div class="table-responsive">
		<div class="table-wrapper">
			<div class="table-title">
				<div class="row">
					<div class="col-sm-6">
						<h2>Manage <b>Employees</b></h2>
					</div>
					<div class="col-sm-6">
            <CustomButton 
              text="Add Employee"
              icon="&#xE147;"
              type="success"
              href="#addEmployeeModal"
              v-on:click="resetStates"
            />
            <CustomButton 
              text="Delete"
              icon="&#xE872;"
              type="danger"
              href="#addEmployeeModal"
            />
					</div>
				</div>
			</div>
			<table class="table table-striped table-hover" >
				<thead>
					<tr>
						<th>
							<span class="custom-checkbox">
								<input type="checkbox" id="selectAll">
								<label for="selectAll"></label>
							</span>
						</th>
						<th>Name</th>
						<th>Email</th>
						<th>Address</th>
						<th>Phone</th>
						<th>Actions</th>
					</tr>
				</thead>
				<tbody v-if="!isLoading">
          <DataRow
            v-for="employee in employees"
            :key="employee.id" 
            :id="employee.id" 
            :name="employee.name" 
            :email="employee.email" 
            :address="employee.address" 
            :phone="employee.phone"/>
				</tbody>
			</table>
      <LoadingSpinner v-if="isLoading"/>
			<div class="clearfix">
				<div class="hint-text">Showing <b>5</b> out of <b>25</b> entries</div>
				<ul class="pagination">
					<li class="page-item disabled"><a href="#">Previous</a></li>
					<li class="page-item"><a href="#" class="page-link">1</a></li>
					<li class="page-item"><a href="#" class="page-link">2</a></li>
					<li class="page-item active"><a href="#" class="page-link">3</a></li>
					<li class="page-item"><a href="#" class="page-link">4</a></li>
					<li class="page-item"><a href="#" class="page-link">5</a></li>
					<li class="page-item"><a href="#" class="page-link">Next</a></li>
				</ul>
			</div>
		</div>
	</div>        
</div>
<!-- Add & Edit Modal HTML -->
<div id="addEmployeeModal" class="modal fade">
	<div class="modal-dialog">
		<div class="modal-content">
			<form>
				<div class="modal-header">						
					<h4 class="modal-title">{{submitFormTitle}}</h4>
					<button id="closeAddModal" type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
				</div>
				<div class="modal-body">					
					<div class="form-group">
						<label>Name</label>
						<input 
              v-model="newEmployee.name"
              type="text" 
              class="form-control" 
              required>
					</div>
					<div class="form-group">
						<label>Email</label>
						<input v-model="newEmployee.email" type="email" class="form-control" required>
					</div>
					<div class="form-group">
						<label>Address</label>
						<textarea v-model="newEmployee.address" class="form-control" required></textarea>
					</div>
					<div class="form-group">
						<label>Phone</label>
						<input v-model="newEmployee.phone" type="text" class="form-control" required>
					</div>					
				</div>
				<div class="modal-footer">
					<input type="button" class="btn btn-default" data-dismiss="modal" value="Cancel">
					<input 
            id="submitButton"
            type="submit" 
            class="btn btn-success" 
            :value="submitButtonText"
            v-on:click="submitForm"> 
				</div>
			</form>
		</div>
	</div>
</div>
<!-- Delete Modal HTML -->
<div id="deleteEmployeeModal" class="modal fade">
	<div class="modal-dialog">
		<div class="modal-content">
			<form>
				<div class="modal-header">						
					<h4 class="modal-title">Delete Employee</h4>
					<button id="deleteModal" type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
				</div>
				<div class="modal-body">					
					<p>Are you sure you want to delete these Records?</p>
					<p class="text-warning"><small>This action cannot be undone.</small></p>
				</div>
				<div class="modal-footer">
					<input type="button" class="btn btn-default" data-dismiss="modal" value="Cancel">
					<CustomButton
            text="Delete"
            type="danger"
            href="#"
            v-on:click="deleteEmployee"
          />
				</div>
			</form>
		</div>
	</div>
</div>
</template>

<script>
import baseUrl from "./config/host"
import CustomButton from "./components/CustomButton"
import LoadingSpinner from "./components/LoadingSpinner"
import DataRow from "./components/DataRow/DataRow"

export default {
  name: 'App',
  components: {
    CustomButton,
    DataRow,
    LoadingSpinner
  },
  data() {
    return {
      submitFormTitle: "Add Employee",
      submitButtonText: "Add",
      employees: [],
      isLoading: false,
      selectedEmployee: null,
      newEmployee: {
        name: "",
        email: "",
        address: "",
        phone: ""
      }
    }
  },
  methods: {
    loadEmployees: async function() {
      this.isLoading = true;
      setTimeout(async () => {
        const resp = await fetch(`${baseUrl}/employees`);
        const data = await resp.json();
        this.employees = data;
        this.isLoading = false;
      }, 1000);
    },
    addEmployee: async function() {
      this.submitButtonText="Add";
      this.submitFormTitle="Add Employee";
      const resp = await fetch(`${baseUrl}/employees`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          name: this.newEmployee.name,
          email: this.newEmployee.email,
          address: this.newEmployee.address,
          phone: this.newEmployee.phone
        })
      });
      const data = await resp.json();
      this.employees.push(data);
      document.getElementById('closeAddModal').click();
    }
    ,
    deleteEmployee: async function() {
      try {
        let id = this.selectedEmployee;
        await fetch(`${baseUrl}/employees/${id}`, {
          method: 'DELETE',
          headers: {
            'Content-Type': 'application/json'
          }
        });
        this.employees = this.employees.filter(item => item.id != id);
        document.getElementById('deleteModal').click();
      } catch(e) {
        console.log('Error', e);
      }
    },
    getSingleEmployee: async function() {
      try {
        const resp = await fetch(`${baseUrl}/employees/${this.selectedEmployee}`);
        const data = await resp.json();
        return data;
      } catch(e) {
        console.log('Error', e);
        return null;
      }
    },
    editEmployee: async function() {
      try {
        await fetch(`${baseUrl}/employees/${this.selectedEmployee}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            name: this.newEmployee.name,
            email: this.newEmployee.email,
            address: this.newEmployee.address,
            phone: this.newEmployee.phone
          })
        });
        document.getElementById('closeAddModal').click();
        this.resetStates();
        this.loadEmployees();
      } catch (error) {
        console.log('Error', error);
      }
    },
    resetStates: function() {
      this.submitButtonText="Add";
      this.submitFormTitle="Add Employee";

      this.selectedEmployee = null;
      this.newEmployee.name = "";
      this.newEmployee.email = "";
      this.newEmployee.address = "";
      this.newEmployee.phone = "";
    },
    submitForm: function() {
      if (this.selectedEmployee) {
        this.editEmployee();
      } else {
        this.addEmployee();
      }
    }
  },
  mounted: function() {
    this.loadEmployees();
  }
}
</script>

<style>
body {
	color: #566787;
	background: #f5f5f5;
	font-family: 'Varela Round', sans-serif;
	font-size: 13px;
}
.table-responsive {
    margin: 30px 0;
}
.table-wrapper {
	background: #fff;
	padding: 20px 25px;
	border-radius: 3px;
	min-width: 1000px;
	box-shadow: 0 1px 1px rgba(0,0,0,.05);
}
.table-title {        
	padding-bottom: 15px;
	background: #435d7d;
	color: #fff;
	padding: 16px 30px;
	min-width: 100%;
	margin: -20px -25px 10px;
	border-radius: 3px 3px 0 0;
}
.table-title h2 {
	margin: 5px 0 0;
	font-size: 24px;
}
.table-title .btn-group {
	float: right;
}
.table-title .btn {
	color: #fff;
	float: right;
	font-size: 13px;
	border: none;
	min-width: 50px;
	border-radius: 2px;
	border: none;
	outline: none !important;
	margin-left: 10px;
}
.table-title .btn i {
	float: left;
	font-size: 21px;
	margin-right: 5px;
}
.table-title .btn span {
	float: left;
	margin-top: 2px;
}
table.table tr th, table.table tr td {
	border-color: #e9e9e9;
	padding: 12px 15px;
	vertical-align: middle;
}
table.table tr th:first-child {
	width: 60px;
}
table.table tr th:last-child {
	width: 100px;
}
table.table-striped tbody tr:nth-of-type(odd) {
	background-color: #fcfcfc;
}
table.table-striped.table-hover tbody tr:hover {
	background: #f5f5f5;
}
table.table th i {
	font-size: 13px;
	margin: 0 5px;
	cursor: pointer;
}	
table.table td:last-child i {
	opacity: 0.9;
	font-size: 22px;
	margin: 0 5px;
}
table.table td a {
	font-weight: bold;
	color: #566787;
	display: inline-block;
	text-decoration: none;
	outline: none !important;
}
table.table td a:hover {
	color: #2196F3;
}
table.table td a.edit {
	color: #FFC107;
}
table.table td a.delete {
	color: #F44336;
}
table.table td i {
	font-size: 19px;
}
table.table .avatar {
	border-radius: 50%;
	vertical-align: middle;
	margin-right: 10px;
}
.pagination {
	float: right;
	margin: 0 0 5px;
}
.pagination li a {
	border: none;
	font-size: 13px;
	min-width: 30px;
	min-height: 30px;
	color: #999;
	margin: 0 2px;
	line-height: 30px;
	border-radius: 2px !important;
	text-align: center;
	padding: 0 6px;
}
.pagination li a:hover {
	color: #666;
}	
.pagination li.active a, .pagination li.active a.page-link {
	background: #03A9F4;
}
.pagination li.active a:hover {        
	background: #0397d6;
}
.pagination li.disabled i {
	color: #ccc;
}
.pagination li i {
	font-size: 16px;
	padding-top: 6px
}
.hint-text {
	float: left;
	margin-top: 10px;
	font-size: 13px;
}    
/* Custom checkbox */
.custom-checkbox {
	position: relative;
}
.custom-checkbox input[type="checkbox"] {    
	opacity: 0;
	position: absolute;
	margin: 5px 0 0 3px;
	z-index: 9;
}
.custom-checkbox label:before{
	width: 18px;
	height: 18px;
}
.custom-checkbox label:before {
	content: '';
	margin-right: 10px;
	display: inline-block;
	vertical-align: text-top;
	background: white;
	border: 1px solid #bbb;
	border-radius: 2px;
	box-sizing: border-box;
	z-index: 2;
}
.custom-checkbox input[type="checkbox"]:checked + label:after {
	content: '';
	position: absolute;
	left: 6px;
	top: 3px;
	width: 6px;
	height: 11px;
	border: solid #000;
	border-width: 0 3px 3px 0;
	transform: inherit;
	z-index: 3;
	transform: rotateZ(45deg);
}
.custom-checkbox input[type="checkbox"]:checked + label:before {
	border-color: #03A9F4;
	background: #03A9F4;
}
.custom-checkbox input[type="checkbox"]:checked + label:after {
	border-color: #fff;
}
.custom-checkbox input[type="checkbox"]:disabled + label:before {
	color: #b8b8b8;
	cursor: auto;
	box-shadow: none;
	background: #ddd;
}
/* Modal styles */
.modal .modal-dialog {
	max-width: 400px;
}
.modal .modal-header, .modal .modal-body, .modal .modal-footer {
	padding: 20px 30px;
}
.modal .modal-content {
	border-radius: 3px;
	font-size: 14px;
}
.modal .modal-footer {
	background: #ecf0f1;
	border-radius: 0 0 3px 3px;
}
.modal .modal-title {
	display: inline-block;
}
.modal .form-control {
	border-radius: 2px;
	box-shadow: none;
	border-color: #dddddd;
}
.modal textarea.form-control {
	resize: vertical;
}
.modal .btn {
	border-radius: 2px;
	min-width: 100px;
}	
.modal form label {
	font-weight: normal;
}	
</style>

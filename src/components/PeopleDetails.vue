<template>
  <div class="main_container" v-if="userData">
      <div class="primary_section">
        <input type="text" placeholder="Search by name" v-model="searchQuery" />
        <button @click="isAddUserModalVisible = true">Add New User</button>
      </div>
      <table>
          <tr>
            <th>Name</th>
            <th>Email</th>
            <th>Mobile</th>
            <th>Date of Birth</th>
            <th>Action</th>
          </tr>
          <tr v-for="(user, index) in filterUsersList" :key="index">
            <td>{{ user.name }}</td>
            <td>{{ user.email }}</td>
            <td>{{ user.mobile }}</td>
            <td>{{ user.dob }}</td>
            <td>
              <div class="action_button">
                <button @click="openEditModal(user)">Edit</button>
                <button @click="openDeleteModal(user)">Delete</button>
              </div>
            </td>
          </tr>
      </table>

      <!-- Delete Modal Start -->
      <div id="myModal" class="modal" v-if="isUserDeleteModal">
        <!-- Modal content -->
        <div class="modal-content">
          <span class="close" @click="closeModal()">&times;</span>
          <!-- <div class="edit_modal_heading">
            <h1></h1>
          </div> -->
          <div class="edit_modal_content">
            Are you sure you want to <b>Delete</b> this user?
          </div>
          <div class="edit_modal_buttons">
            <button class="discard_button" @click="closeModal()">Discard</button>
            <button class="blue_button" @click="deleteUser()">Confirm</button>
          </div>
        </div>
      </div>

      <!-- Add Modal Start -->
      <div id="myModal" class="modal" v-if="isAddUserModalVisible">
        <!-- Modal content -->
        <div class="modal-content">
          <span class="close" @click="closeModal()">&times;</span>
          <div class="edit_modal_heading">
            <h1>Add new users Details</h1>
          </div>
          <ValidationObserver v-slot="{ handleSubmit }">
            <div class="edit_modal_content">
              <div class="edit_modal_inner_content">
                <ValidationProvider name="Name" rules="required" v-slot="{ errors }">
                  <label>Name</label>
                  <br/>
                  <input type="text" v-model="newUserDetails.name" />
                  <span class="d-block">{{ errors[0] }}</span>
                </ValidationProvider>
              </div>

              <div class="edit_modal_inner_content">
                <ValidationProvider name="Email" rules="required" v-slot="{ errors }">
                  <label>Email</label>
                  <br/>
                  <input type="email" v-model="newUserDetails.email" />
                  <span class="d-block">{{ errors[0] }}</span>
                </ValidationProvider>
              </div>

              <div class="edit_modal_inner_content">
                <ValidationProvider name="Mobile" rules="required" v-slot="{ errors }">
                  <label>Mobile</label>
                  <br/>
                  <input type="phone" v-model="newUserDetails.mobile" />
                  <span class="d-block">{{ errors[0] }}</span>
                </ValidationProvider>
              </div>

              <div class="edit_modal_inner_content">
                <ValidationProvider name="Date of Birth" rules="required" v-slot="{ errors }">
                  <label>Date of Birth</label>
                  <br/>
                  <input type="date" v-model="newUserDetails.dob" />
                  <span class="d-block">{{ errors[0] }}</span>
                </ValidationProvider>
              </div>
            </div>
            <div class="edit_modal_buttons">
              <button class="discard_button"  @click="closeModal()">Discard</button>
              <button class="blue_button" @click="handleSubmit(saveNewUserData)">Save</button>
            </div>
          </ValidationObserver>
        </div>
      </div>

      <!-- Edit Modal Start -->
      <div id="myModal" class="modal" v-if="isEditModalVisible">
        <!-- Modal content -->
        <div class="modal-content">
          <span class="close" @click="closeModal()">&times;</span>
          <div class="edit_modal_heading">
            <h1>Add or edit</h1>
          </div>
          <div class="edit_modal_content">
            <div class="edit_modal_inner_content">
              <label>Name</label>
              <br/>
              <input type="text" v-model="editModalData.name" />
            </div>

            <div class="edit_modal_inner_content">
              <label>Email</label>
              <br/>
              <input type="email" v-model="editModalData.email" />
            </div>

            <div class="edit_modal_inner_content">
              <label>Mobile</label>
              <br/>
              <input type="phone" pattern=”[0-9]{3}-[0-9]{3}-[0-9]{4}” v-model="editModalData.mobile" />
            </div>

            <div class="edit_modal_inner_content">
              <label>Date of Birth</label>
              <br/>
              <input type="date" v-model="editModalData.dob" />
            </div>
          </div>
          <div class="edit_modal_buttons">
            <button class="discard_button" @click="closeModal()">Discard</button>
            <button class="blue_button" @click="updateUserData()">Save</button>
          </div>
        </div>
      </div>
  </div>
</template>
<script>
import axios from "axios";
import env from "@/../env.js"
export default {
  name : "HomePage",
  data() {
    return {
      isEditModalVisible : false,
      isAddUserModalVisible: false,
      isUserDeleteModal: false,
      userData: [],
      editModalData: [],
      newUserDetails: {
        "name" : "",
        "email" : "",
        "mobile" : "",
        "dob" : "",
      },
      searchQuery: "",
      deleteModalData: []
    }
  },
  async mounted() {
    await this.getUsers();
  },
  methods: {
    openDeleteModal(user) {
      this.isUserDeleteModal = true;
      this.deleteModalData = user;
    },
    deleteUser() {
      axios.delete(env.API_URL + "/users/" + this.deleteModalData._id).then(() => {
        this.closeModal();
        this.getUsers();
      }).catch(error => {
        console.log(error)
      });
    },
    getUsers(){
      axios.get(env.API_URL + "/users").then(res => {
        this.userData = res.data.users;
      }).catch(error => {
        console.log(error)
      });
    },
    openEditModal(user) {
      this.isEditModalVisible = true;
      this.editModalData = user;
    },
    closeModal() {
      this.isEditModalVisible = false;
      this.isAddUserModalVisible = false;
      this.isUserDeleteModal = false;
    },
    updateUserData() {
      let data = {
        "name" : this.editModalData.name,
        "email" : this.editModalData.email,
        "mobile" : this.editModalData.mobile,
        "dob" : this.editModalData.dob,
      }
      axios.post(env.API_URL + "/users/" + this.editModalData._id, data, {
        headers: {'Content-Type': 'application/x-www-form-urlencoded'},
      }).then(() => {
        this.closeModal();
        this.$swal.fire({
          icon: "success",
          text: "User Updated Successfully",
          showConfirmButton: true,
        });
      }).catch(error => {
        console.log(error)
      });
    },
    saveNewUserData() {
      let data = {
        "name" : this.newUserDetails.name,
        "email" : this.newUserDetails.email,
        "mobile" : this.newUserDetails.mobile,
        "dob" : this.newUserDetails.dob,
      }
      axios.post(env.API_URL + "/users", data, {
        headers: {'Content-Type': 'application/x-www-form-urlencoded'},
      }).then(async (res) => {
        console.log(res)
        this.closeModal();
        this.$swal.fire({
          icon: "success",
          text: "New User Added Successfully",
          showConfirmButton: true,
        });
        await this.getUsers();
      }).catch(error => {
        console.log(error)
      });
    }
  },
  computed: {
    filterUsersList() {
      if(this.searchQuery.charAt(3) != '') {
        var filterData =[]
        this.userData.forEach(data => {
          if (data.name.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
            data.email.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
            data.mobile.includes(this.searchQuery)) {
            filterData.push(data);
          }
        })
        return filterData;
      } else {
        return this.userData;
      }
    }
  }
}
</script>
<style scoped>
button{
  cursor: pointer;
}
.discard_button {
  background-color: #E3E4E5;
  border: #E3E4E5;
  color: black;
  font-size: 15px;
  font-weight: 600;
  width: 80px;
  height: 30px
}
.blue_button {
  background-color: #3CB6DE;
  border: #3CB6DE;
  color: white;
  font-size: 15px;
  font-weight: 600;
  width: 80px;
  height: 30px
}
.action_button {
  display: flex;
  justify-content: space-around;
}
.action_button button {
  color: white;
  font-size: 15px;
  font-weight: 600;
}
.main_container {
  width: 1000px;
  margin: auto;
}

.action_button button:first-child {
  background-color: #3CB6DE;
  border: #3CB6DE;
}
.action_button button:nth-child(2) {
  background-color: red;
  border: red;
}

.primary_section {
  display: flex;
  justify-content: space-between;
  margin-bottom: 1rem;
  height: 30px;;
}
.primary_section input {
  width: 220px;
}

.primary_section button {
  background-color: #3CB6DE;
  border: #3CB6DE;
  color: white;
  font-size: 15px;
  font-weight: 600;
}
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td, th {
  border: 2px solid black;
  text-align: left;
  padding: 8px;
}
th {
  font-size: 20px;
  color: black;
}

/* Modal CSS */
.modal {
  display: block;
  position: fixed;
  z-index: 1;
  padding-top: 100px;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgb(0,0,0);
  background-color: rgba(0,0,0,0.4);
}

.modal-content {
  background-color: #fefefe;
  margin: auto;
  padding: 20px;
  border: 1px solid #888;
  width: 35%;
  border-radius: 20px;
  border: 2px solid white;
}

.close {
  color: #aaaaaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: #000;
  text-decoration: none;
  cursor: pointer;
}
.edit_modal_content {
  text-align: left;

}
.edit_modal_inner_content {
  margin: 10px 0;
}

.edit_modal_inner_content label {
  font-size: larger;
  color: #8E9091;
  font-weight: 600;
}

.edit_modal_inner_content input {
  margin-top: 10px;
  width: 100%;
  height: 25px;
}
.edit_modal_buttons {
  margin-top: 2rem;
  text-align: right;
}
.edit_modal_buttons button {
  margin: 0 10px;
}
.d-block {
  display: block;
  color: red;
}
</style>
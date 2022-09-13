<template>
  <div>
    <b-card>
      <div class="container">
        <b-col class="customCol">
          <b-button variant="primary" class="text-right" @click="addUser(user)"
            >Add New</b-button
          >
        </b-col>
        <b-card class="mt-3" header="Users">
          <b-table-simple hover small caption-top responsive>
            <b-thead head-variant="dark">
              <b-tr>
                <b-th>#</b-th>
                <b-th>Name</b-th>
                <b-th>Email</b-th>
                <b-th>DOB</b-th>
                <b-th>Favorite Color</b-th>
                <b-th>Action</b-th>
              </b-tr>
            </b-thead>
            <b-tbody v-if="usersData.length">
              <b-tr
                v-for="(user, index) in usersData
                  ? usersData.slice((page - 1) * 10, page * 10)
                  : []"
                :key="index"
              >
                <b-td>{{ index + 1 }}</b-td>
                <b-td>{{ user.name }}</b-td>
                <b-td>{{ user.email }}</b-td>
                <b-td>{{ dateFormat(user.dob, "dd-MM-yyyy") }}</b-td>
                <b-td>{{ user.color }}</b-td>
                <b-td
                  ><b-button class="m-1" variant="info" @click="editUser(user)"
                    >Edit</b-button
                  ><b-button
                    class="m-1"
                    variant="danger"
                    @click="deleteUser(user.id)"
                    >Delete</b-button
                  >
                </b-td>
              </b-tr>
            </b-tbody>
            <b-tbody v-else>
              <b-tr>
                <b-td colspan="6">No data available in table</b-td>
              </b-tr>
            </b-tbody>
          </b-table-simple>
          <Pagination
            :page="page"
            :totalPages="paginationC"
            name="users"
            :count="data_count ? data_count : 0"
            :incrementpage="incrementpage"
            :decrementpage="decrementpage"
            :setpage="setpage"
            perpage="10"
          />
          <UserModel
            v-if="displayModal"
            @close-modal-event="hideModal"
            @update-modal-event="updateData"
            @add-modal-event="onSubmit"
            :userDetails="userDetails"
            :title="popupTitle"
            :errors="errors"
          />
        </b-card>
      </div>
    </b-card>
  </div>
</template>

<script>
import axios from "axios";
import Pagination from "../components/Pagination.vue";
import UserModel from "./UserModel.vue";

export default {
  name: "add-tutorial",
  components: {
    Pagination,
    UserModel,
  },
  data() {
    return {
      paginationC: [],
      form: {
        email: null,
        name: null,
        dob: null,
        color: null,
      },
      valid: true,
      errors: {},
      usersData: [],
      userDetails: [],
      submitted: false,
      page: 1,
      data_count: 0,
      displayModal: false,
      popupTitle: "Add",
    };
  },
  async mounted() {
    await this.getallUsers();
  },

  methods: {
    async onSubmit(form) {
      const host = process.env.VUE_APP_BACKEND_URL;
      var name = form.name;
      var email = form.email;
      var dob = form.dob;
      var color = form.color;
      // Validations
      const validName = this.validateName(name);
      this.errors = {};
      this.valid = true;
      this.errors.name = validName.error;
      if (this.valid) {
        this.valid = validName.valid;
      }
      const validEmail = this.validateEmail(email);
      this.errors.email = validEmail.error;
      if (this.valid) {
        this.valid = validEmail.valid;
      }
      if (this.valid) {
        axios.defaults.headers.post["Content-Type"] = "application/json";
        await axios
          .post(host + "add/user", {
            name: name,
            email: email,
            dob: dob,
            color: color,
          })
          .then((res) => {
            this.getallUsers();
            this.hideModal();
            if (res.data.status == true) {
              this.$swal.fire({
                icon: "success",
                title: "User has been create successfully..",
                showConfirmButton: false,
                timer: 1500,
              });
            } else {
              this.$swal.fire({
                icon: "error",
                title: "Oops...",
                text: "Something went wrong!",
                timer: 1500,
              });
            }
          })
          .catch((error) => {
            "email" in error.response.data
              ? (this.errors.email = error.response.data.email[0])
              : (this.errors.email = "");
            "name" in error.response.data
              ? (this.errors.name = error.response.data.name[0])
              : (this.errors.name = "");
            "color" in error.response.data
              ? (this.errors.color = error.response.data.color[0])
              : (this.errors.color = "");
            "dob" in error.response.data
              ? (this.errors.dob = error.response.data.dob[0])
              : (this.errors.dob = "");
          });
      }
    },

    async getallUsers() {
      const host = process.env.VUE_APP_BACKEND_URL;
      let result = await axios.get(host + "users");

      this.paginationC = Array(Math.ceil(result.data.data.length / 10))
        .fill(0)
        .map((e, i) => i + 1);

      this.data_count = result?.data?.data ? result.data.data.length : 0;
      this.usersData = result.data.data;
      return result.data.data;
    },

    editUser(user) {
      this.displayModal = true;
      this.errors = {};
      this.userDetails = user;
      this.popupTitle = "Edit";
    },

    addUser() {
      this.userDetails = [];
      this.errors = {};
      this.displayModal = true;
      this.popupTitle = "Add";
    },

    hideModal() {
      this.displayModal = false;
    },

    updateData(form) {
      const host = process.env.VUE_APP_BACKEND_URL;
      var name = form.name;
      var email = form.email;
      var dob = form.dob;
      var color = form.color;
      var id = form.id;
      // Validations
      const validName = this.validateName(name);
      this.errors = {};
      this.valid = true;
      this.errors.name = validName.error;
      if (this.valid) {
        this.valid = validName.valid;
      }
      const validEmail = this.validateEmail(email);
      this.errors.email = validEmail.error;
      if (this.valid) {
        this.valid = validEmail.valid;
      }

      if (this.valid) {
        axios.defaults.headers.post["Content-Type"] = "application/json";
        axios
          .post(host + "update/user/" + id, {
            name: name,
            email: email,
            dob: dob,
            color: color,
          })
          .then((res) => {
            this.getallUsers();
            this.hideModal();
            if (res.data.status == true) {
              this.$swal.fire({
                icon: "success",
                title: "User has been updated successfully..",
                showConfirmButton: false,
                timer: 1500,
              });
            } else {
              this.$swal.fire({
                icon: "error",
                title: "Oops...",
                text: "Something went wrong!",
                timer: 1500,
              });
            }
          })
          .catch((error) => {
            "email" in error.response.data
              ? (this.errors.email = error.response.data.email[0])
              : (this.errors.email = "");
            "name" in error.response.data
              ? (this.errors.name = error.response.data.name[0])
              : (this.errors.name = "");
            "color" in error.response.data
              ? (this.errors.color = error.response.data.color[0])
              : (this.errors.color = "");
            "dob" in error.response.data
              ? (this.errors.dob = error.response.data.dob[0])
              : (this.errors.dob = "");
          });
      }
    },

    deleteUser(userId) {
      const host = process.env.VUE_APP_BACKEND_URL;
      this.$swal
        .fire({
          title: "Are you sure?",
          text: "You won't be able to revert this!",
          icon: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "Yes, delete it!",
          preConfirm: () => {
            return axios
              .post(host + "delete/user/" + userId)
              .then((res) => {
                if (res.status != 200) {
                  throw new Error("Something went wrong");
                }
                return res.data;
              })
              .catch((error) => {
                this.$swal.showValidationMessage(`Request failed: ${error}`);
              });
          },
        })
        .then((result) => {
          if (result.isConfirmed) {
            this.getallUsers();
            this.$swal.fire(
              "Deleted!",
              "Your record has been deleted.",
              "success"
            );
          }
        });
    },

    dateFormat(inputDate, format) {
      const date = new Date(inputDate);
      const day = date.getDate();
      const month = date.getMonth() + 1;
      const year = date.getFullYear();
      format = format.replace("MM", month.toString().padStart(2, "0"));
      if (format.indexOf("yyyy") > -1) {
        format = format.replace("yyyy", year.toString());
      } else if (format.indexOf("yy") > -1) {
        format = format.replace("yy", year.toString().substr(2, 2));
      }
      format = format.replace("dd", day.toString().padStart(2, "0"));
      return format;
    },

    incrementpage() {
      this.page = this.page + 1;
    },
    //go to previous oage
    decrementpage() {
      this.page = this.page - 1;
    },
    //go to perticular page
    setpage(page) {
      this.page = page;
    },
    validateName(name) {
      if (name == undefined || name == "") {
        return { valid: false, error: "This field is required" };
      }
      return { valid: true, error: null };
    },
    validateEmail(email) {
      if (email == undefined || email == "") {
        return { valid: false, error: "This field is required" };
      }
      // if (!email.match(/^\w+([.-]?\w+)_@\w+(_[_.-]?\w+)_(.\w{2,3})+$/)) {
      //   return { valid: false, error: "Please, enter a valid email." };
      // }
      return { valid: true, error: null };
    },
  },
};
</script>


<style>
.main-pagination .pagination {
  display: flex;
  justify-content: center;
}

.main-pagination .pagination button {
  width: 35px;
  height: 35px;
  border: 2px solid #000;
  line-height: 30px;
  margin: 0 10px;
}

.main-pagination .pagination button.page-link-active {
  background: #000;
  color: #fff;
}

.error {
  color: red;
}

.text-right {
  text-align: right;
}

.customCol {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  height: calc(50% + 19px);
}
</style>
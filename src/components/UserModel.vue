<template>
  <div class="bootstrap-modal-no-jquery">
    <div class="modal" tabindex="-1" role="dialog">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">{{ title }} User</h5>
            <b-button @click="closeModal()" class="close"
              ><span aria-hidden="true">&times;</span></b-button
            >
          </div>
          <b-form @submit="onSubmit" @reset="onReset" class="text-left">
            <div class="modal-body">
              <b-form-group
                id="input-group-1"
                label="Your Name:"
                label-for="input-1"
              >
                <b-form-input
                  id="input-1"
                  v-model="form.name"
                  placeholder="Enter name"
                  required
                ></b-form-input>
                <div class="error">{{ errors.name }}</div>
              </b-form-group>
              <b-form-group id="input-group-2" label="DOB:" label-for="input-2">
                <b-form-input
                  id="input-2"
                  v-model="form.dob"
                  type="date"
                  placeholder="Enter dob"
                  required
                ></b-form-input>
                <div class="error">{{ errors.dob }}</div>
              </b-form-group>

              <b-form-group
                id="input-group-3"
                label="Email address:"
                label-for="input-3"
              >
                <b-form-input
                  id="input-3"
                  v-model="form.email"
                  type="email"
                  placeholder="Enter email"
                  required
                ></b-form-input>
                <div class="error">{{ errors.email }}</div>
              </b-form-group>

              <b-form-group
                id="input-group-4"
                label="Favorite Color:"
                label-for="input-4"
              >
                <div class="colorPicker">
                  <b-form-input
                    id="input-5"
                    type="text"
                    readonly=""
                    disabled=""
                    v-model="form.color"
                    class="mr-2"
                  ></b-form-input>
                  <b-form-input
                    id="input-4"
                    v-model="form.color"
                    type="color"
                    placeholder="Enter favorite color"
                    required
                  ></b-form-input>
                </div>
              </b-form-group>
              <div class="error">{{ errors.color }}</div>
            </div>
            <div class="modal-footer">
              <button
                type="button"
                class="btn btn-primary"
                @click="saveChanges"
              >
                Save changes
              </button>
              <button
                type="button"
                class="btn btn-secondary"
                @click="closeModal"
              >
                Close
              </button>
            </div>
          </b-form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";
export default {
  name: "UserModel",
  components: {},
  data() {
    return {
      form: {
        email: null,
        name: null,
        dob: new Date(),
        color: null,
        id: null,
      },
    };
  },
  props: {
    userDetails: [],
    title: null,
    errors: {},
  },
  watch: {
    errors: function () {},
  },
  created() {
    this.getValue();
  },
  methods: {
    closeModal() {
      this.$emit("close-modal-event");
    },
    saveChanges() {
      this.title == "Edit"
        ? this.$emit("update-modal-event", this.form)
        : this.$emit("add-modal-event", this.form);
    },
    getValue() {
      this.form.email = this.userDetails.email;
      this.form.name = this.userDetails.name;
      this.form.dob = moment(this.userDetails.dob).format("YYYY-MM-DD");
      this.form.color = this.userDetails.color;
      this.form.id = this.userDetails.id;
    },
  },
};
</script>

<style scoped>
.modal {
  display: block;
}
.text-left {
  text-align: left;
}
.colorPicker {
  display: flex;
}
#input-5 {
  margin-right: 10px;
}
</style>
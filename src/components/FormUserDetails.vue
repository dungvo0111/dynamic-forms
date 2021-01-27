<template>
  <div>
    <h1 class="title">Create Account</h1>

    <h2 class="subtitle">
      Create an account or log in to order your liquid gold subscription
    </h2>

    <form @input="updateInput" class="form">
      <div class="form-group">
        <label class="form-label" for="email">Email</label>
        <input
          type="text"
          v-model="form.email"
          @blur="$v.form.email.$touch()"
          placeholder="your@email.com"
          class="form-control"
          id="email"
        />
        <div
          v-if="$v.form.email.$error && !$v.form.email.required"
          class="error"
        >
          email is required
        </div>
        <div v-if="$v.form.email.$error && !$v.form.email.email" class="error">
          email is invalid
        </div>
      </div>

      <div class="form-group">
        <label class="form-label" for="password">Password</label>
        <input
          v-model="$v.form.password.$model"
          type="password"
          placeholder="Super Secret Password"
          class="form-control"
          id="password"
        />
        <div
          v-if="$v.form.password.$error && !$v.form.password.required"
          class="error"
        >
          password is required
        </div>
      </div>

      <div class="form-group">
        <label class="form-label" for="name">Name</label>
        <input
          v-model="$v.form.name.$model"
          type="text"
          placeholder="What should we call you?"
          class="form-control"
          id="name"
        />
        <div v-if="$v.form.name.$error" class="error">name is required</div>
      </div>
    </form>
  </div>
</template>

<script>
import { required, email } from "vuelidate/lib/validators";
export default {
  props: {
    formData: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      form: {
        email: this.formData.email,
        password: this.formData.password,
        name: this.formData.name,
      },
    };
  },
  validations: {
    form: {
      email: {
        required,
        email,
      },
      password: {
        required,
      },
      name: {
        required,
      },
    },
  },
  methods: {
    updateInput() {
      if (!this.$v.$invalid) {
        this.$emit("updateData", {
          name: this.form.name,
          email: this.form.email,
          password: this.form.password,
        });
      }
    },
  },
};
</script>

<style scoped>
</style>
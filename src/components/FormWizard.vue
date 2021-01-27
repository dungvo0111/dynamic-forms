<template>
  <div>
    <div v-if="currentStepNumber <= length" v-show="asyncState !== 'pending'">
      <keep-alive>
        <component
          ref="currentStep"
          :is="currentStep"
          @updateData="updateData"
          :formData="form"
        ></component>
      </keep-alive>
      <div class="progress-bar">
        <div :style="`width: ${progress}%;`"></div>
      </div>

      <!-- Actions -->
      <div class="buttons">
        <button
          @click="goBack"
          v-if="currentStepNumber > 1"
          class="btn-outlined"
        >
          Back
        </button>
        <button @click="goNext" class="btn">
          {{ lastStep ? "Submit" : "Next" }}
        </button>
      </div>
    </div>
    <div v-else>
      <h1 class="title">Thank you!</h1>
      <h2 class="subtitle">We look forward to shipping you your first box!</h2>

      <p class="text-center">
        <a href="https://vueschool.io" target="_blank" class="btn"
          >Go somewhere cool!</a
        >
      </p>
    </div>
    <div class="loading-wrapper" v-if="asyncState === 'pending'">
      <div class="loader">
        <img src="/spinner.svg" alt="" />
        <p>Please wait, we're hitting our servers!</p>
      </div>
    </div>
  </div>
</template>

<script>
import FormPlanPicker from "./FormPlanPicker";
import FormUserDetails from "./FormUserDetails";
import FormAddress from "./FormAddress";
import FormReviewOrder from "./FormReviewOrder";
import { postFormToDB } from "@/api/index";
export default {
  name: "FormWizard",
  components: {
    FormPlanPicker,
    FormUserDetails,
    FormAddress,
    FormReviewOrder,
  },
  data() {
    return {
      currentStepNumber: 1,
      form: {
        plan: null,
        email: null,
        name: null,
        password: null,
        address: null,
        recipient: null,
        chocolate: false,
        otherTreat: false,
      },
      steps: [
        "FormPlanPicker",
        "FormUserDetails",
        "FormAddress",
        "FormReviewOrder",
      ],
      asyncState: null,
    };
  },
  computed: {
    progress() {
      return (this.currentStepNumber / this.length) * 100;
    },
    currentStep() {
      return this.steps[this.currentStepNumber - 1];
    },
    length() {
      return this.steps.length;
    },
    lastStep() {
      return this.currentStepNumber === this.length;
    },
  },
  methods: {
    goBack() {
      if (!this.$refs.currentStep.$v.$error) {
        this.currentStepNumber--;
      }
    },
    goNext() {
      if (this.lastStep) {
        this.asyncState = "pending";
        postFormToDB(this.form).then((resp) => {
          console.log("Form submitted!", this.form);
          this.asyncState = "success";
          this.currentStepNumber++;
        });
      } else {
        this.$refs.currentStep.$v.$touch();
        if (!this.$refs.currentStep.$v.$invalid) {
          this.currentStepNumber++;
        }
      }
    },
    updateData(data) {
      this.form = { ...this.form, ...data };
    },
  },
};
</script>

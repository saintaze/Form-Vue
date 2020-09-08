<template>
  <div class="signup">
    <h1>Sign Up</h1>
    <h2 v-if="formSubmitSuccess">Form is submitted successfully</h2>
    <form @submit.prevent="formSubmit">
      <!-- First Name -->
      <div class="form-group" :class="{ 'form-group--error': '' }">
        <label class="form__label">First Name</label>
        <input class="form__input" v-model.trim="data.firstName" @input="validateFirstName"/>
      </div>
      <div v-if="$v.data.firstName.$error">
        <div class="error" v-if="!$v.data.firstName.required">Field is required</div>
        <div class="error" v-if="!$v.data.firstName.alpha">First Name must consist of letters.</div>
      </div>

      <!-- Last Name -->
      <div class="form-group" :class="{ 'form-group--error': '' }">
        <label class="form__label">Last Name</label>
        <input class="form__input" v-model.trim="data.lastName" @input="validateLastName"/>
      </div>
      <div v-if="$v.data.lastName.$error">
        <div class="error" v-if="!$v.data.lastName.required">Field is required</div>
        <div class="error" v-if="!$v.data.lastName.alpha">Last Name must consist of letters.</div>
      </div>
      
      <!-- Email -->
      <div class="form-group" :class="{ 'form-group--error': '' }">
        <label class="form__label">Email</label>
        <input class="form__input" v-model.trim="data.email" @input="validateEmail"/>
      </div>
      <div v-if="$v.data.email.$error">
        <div class="error" v-if="!$v.data.email.required">Field is required</div>
        <div class="error" v-if="!$v.data.email.email">Email must be valid</div>
        <div class="error" v-if="!$v.data.email.isUnique && $v.data.email.email">Email is taken</div>
      </div>

      <!-- Password -->
      <div class="form-group" :class="{ 'form-group--error': '' }">
        <label class="form__label">Password</label>
        <input class="form__input" v-model.trim="data.password" @input="validatePassword"/>
      </div>
      <div v-if="$v.data.password.$error">
        <div class="error" v-if="!$v.data.password.required">Field is required</div>
        <div class="error" v-if="!$v.data.password.minLength">Password must be minimim of {{$v.data.password.$params.minLength.min}} of characters</div>
      </div>

      <input type="submit" >

    </form>
  </div>
</template>

<script>
import { required, minLength, alpha, email } from "vuelidate/lib/validators";
import axios from "axios";

const SIGNUP_URL = "https://api.raisely.com/v3/signup";
const EMAIL_VALIDATE_URL = "https://api.raisely.com/v3/check-user";

export default {
  name: "signup",
  data: () => ({
    formSubmitSuccess: false,
    data: {
      firstName: "",
      lastName: "",
      email: "",
      password: ""
    }
  }),
  validations: {
    data: {
      firstName: { required, alpha },
      lastName: { required, alpha },
      password: { required, minLength: minLength(8) },
      email: { 
        required, 
        email,
        async isUnique(email){
          if(email === '') return true
          if(this.$v.data.email.email) {
            const payload = {
              "campaignUuid": "46aa3270-d2ee-11ea-a9f0-e9a68ccff42a",
              "data": { "email": this.data.email }
            }
            const res = await axios.post(EMAIL_VALIDATE_URL, payload);
            console.log(res.data.data.status)
            return res.data.data.status === 'OK'
          }
        }
      }
    }
  },
  methods: {
    async formSubmit() {
      this.$v.$touch();
      if (!this.$v.$invalid) {
        console.log("form is valid");
        const payload = {
          "campaignUuid": "46aa3270-d2ee-11ea-a9f0-e9a68ccff42a",
          data: this.data
        }
        const res = await axios.post(SIGNUP_URL, payload);
        this.formSubmitSuccess = true
        console.log(res)
      }
    }, 
    validateFirstName(value) {
      this.$v.data.firstName.$touch();
    },
    validateLastName(value) {
      this.$v.data.lastName.$touch();
    },
    validatePassword(value){
      this.$v.data.password.$touch();
    },
    validateEmail(value){
      this.$v.data.email.$touch();
    }
  },
};
</script>

<style lang="scss">
.signup {
  height: 100vh;
  background-color: #f2f2f2;
  display: grid;
  place-items: center;
}

small {
  display: block;
}
</style>









































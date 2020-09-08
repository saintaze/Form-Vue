<template>
  <div class="signup">
    <h1>Sign Up</h1>
    <h2 v-if="formSubmitSuccess">Form is submitted successfully</h2>
    <form class="form" @submit.prevent="formSubmit">

      <!-- First Name -->
      <div class="form__group" :class="{ 'form__group--error': $v.data.firstName.$error }">
        <label class="form__label">First Name</label>
        <input class="form__input" v-model.trim="data.firstName" @input="validateFirstName"/>
      </div>
      <div class="form__errors" v-if="$v.data.firstName.$error">
        <div class="form__error" v-if="!$v.data.firstName.required">First Name is required.</div>
        <div class="form__error" v-if="!$v.data.firstName.alpha">First Name must only consist of letters.</div>
      </div>

      <!-- Last Name -->
      <div class="form__group" :class="{ 'form__group--error': $v.data.lastName.$error }">
        <label class="form__label">Last Name</label>
        <input class="form__input" v-model.trim="data.lastName" @input="validateLastName"/>
      </div>
      <div class="form__errors" v-if="$v.data.lastName.$error">
        <div class="form__error" v-if="!$v.data.lastName.required">Last Name is required.</div>
        <div class="form__error" v-if="!$v.data.lastName.alpha">Last Name must only consist of letters.</div>
      </div>
      
      <!-- Email -->
      <div class="form__group" :class="{ 'form__group--error': $v.data.email.$error }">
        <label class="form__label">Email</label>
        <input class="form__input" v-model.trim="data.email" @input="validateEmail"/>
      </div>
      <div class="form__errors" v-if="$v.data.email.$error">
        <div class="form__error" v-if="!$v.data.email.required">Email is required.</div>
        <div class="form__error" v-if="!$v.data.email.email">Email must be valid.</div>
        <div class="form__error" v-if="!$v.data.email.isUnique && $v.data.email.email">Email is taken.</div>
      </div>

      <!-- Password -->
      <div class="form__group" :class="{ 'form__group--error': $v.data.password.$error }">
        <label class="form__label">Password</label>
        <input class="form__input" v-model.trim="data.password" @input="validatePassword"/>
      </div>
      <div class="form__errors" v-if="$v.data.password.$error">
        <div class="form__error" v-if="!$v.data.password.required">Password is required</div>
        <div class="form__error" v-if="!$v.data.password.minLength">Password must have atleast {{$v.data.password.$params.minLength.min}} characters.</div>
      </div>

      <input class="form__submit" type="submit" >

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

.form {

  &__group {
    margin-bottom: 2rem;

    &--error {
      animation: shakeError .6s ease-in-out forwards;
    }

    &--error .form__label {
      color: #f04124;
    }

    &--error .form__input {
      border-color: #f79483;
    }
  }

  

  &__input {
    outline: none;
    font-family: "Lato", sans-serif;
    font-size: 0.875rem;
    font-weight: 300;
    color: #374853;
    line-height: 2.375rem;
    min-height: 2.375rem;
    position: relative;
    border: 1px solid #E8E8E8;
    border-radius: 5px;
    background: #fff;
    padding: 0 0.8125rem;
    width: 100%;
    transition: border .1s ease;
    box-sizing: border-box;
  }

  &__label {
    font-size: 0.8125rem;
    color: #4b6372;
    margin-bottom: 0.3125rem;
    margin-left: 0.875rem;
    display: block;
    font-family: "Lato", sans-serif;
  }


  &__error {
    font-size: 0.75rem;
    line-height: 1;
    color: #f57f6c;
    margin-left: 14px;
    margin-top: -1.6875rem;
    margin-bottom: 0.9375rem;
  }


  &__submit {
    cursor: pointer;
    font-family: "Lato", sans-serif;
    font-size: 0.875rem;
    font-weight: 300;
    color: #fff;
    min-height: 2.5rem;
    line-height: 1.4;
    padding: 0.5rem 1.875rem 0.625rem;
    box-sizing: border-box;
    position: relative;
    display: inline-block;
    background: #41B883;
    border: none;
    border-radius: 5px;
    z-index: 1;
    overflow: hidden;
    outline: none;
    vertical-align: middle;
    transition: 0.1s background ease, 0.1s border-color ease, 0.1s color ease;
    border-bottom: 3px solid #266d4d;
    text-decoration: none;

    &:hover {
      background: #349268;
    }
  }

  &__errors {

  }

}



@keyframes shakeError {
  0% {
    transform: translateX(0); }
  15% {
    transform: translateX(0.375rem); }
  30% {
    transform: translateX(-0.375rem); }
  45% {
    transform: translateX(0.375rem); }
  60% {
    transform: translateX(-0.375rem); }
  75% {
    transform: translateX(0.375rem); }
  90% {
    transform: translateX(-0.375rem); }
  100% {
    transform: translateX(0); } 
  }
</style>









































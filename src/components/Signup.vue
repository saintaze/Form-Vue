<template>
  <div class="signup">

    <div class="signup__left">
      <div class="signup__center">
        <h2 class="signup__header signup__header--grey">Sign Up</h2>        
        <form class="form" @submit.prevent="formSubmit">

          <!-- First Name -->
          <div class="form__group" :class="{ 'form__group--error': $v.data.firstName.$error }">
            <label class="form__label">First Name</label>
            <input class="form__input" v-model.trim="data.firstName" @input="validateFirstName" placeholder="Enter First Name"/>
            
          </div>
          <div class="form__errors" >
            <div class="form__error" v-if="!$v.data.firstName.required">First Name is required.</div>
            <div class="form__error" v-if="!$v.data.firstName.alpha">First Name must only consist of letters.</div>
          </div>

          <!-- Last Name -->
          <div class="form__group" :class="{ 'form__group--error': $v.data.lastName.$error }">
            <label class="form__label">Last Name</label>
            <input class="form__input" v-model.trim="data.lastName" @input="validateLastName" placeholder="Enter Last Name"/>
            
          </div>
          <div class="form__errors" >
            <div class="form__error" v-if="!$v.data.lastName.required">Last Name is required.</div>
            <div class="form__error" v-if="!$v.data.lastName.alpha">Last Name must only consist of letters.</div>
          </div>
          
          <!-- Email -->
          <div class="form__group" :class="{ 'form__group--error': $v.data.email.$error }">
            <label class="form__label">Email</label>
            <input class="form__input" v-model.trim="data.email" @input="validateEmail" placeholder="Enter Password"/>
            <i class="form__icon far fa-envelope"></i>
          </div>
          <div class="form__errors">
            <div class="form__error" v-if="!$v.data.email.required">Email is required.</div>
            <div class="form__error" v-if="!$v.data.email.email">Email must be valid.</div>
            <div class="form__error" v-if="!$v.data.email.isUnique && $v.data.email.email">Email is taken.</div>
          </div>

          <!-- Password -->
          <div class="form__group" :class="{ 'form__group--error': $v.data.password.$error }">
            <label class="form__label">Password</label>
            <input class="form__input" v-model.trim="data.password" @input="validatePassword" placeholder="Enter Email"/>
            <i class="form__icon fas fa-eye form__icon"></i>
          </div>
          <div class="form__errors" >
            <div class="form__error" v-if="!$v.data.password.required">Password is required</div>
            <div class="form__error" v-if="!$v.data.password.minLength">Password must have atleast {{$v.data.password.$params.minLength.min}} characters.</div>
          </div>

          <input class="form__submit" type="submit" >

        
        </form>
      </div>
    </div>

    <div class="signup__right">
      <h2 class="signup__header signup__header--white">
          <span :class="{'move-right': formSubmitSuccess}" class="signup__header-message--1">Welcome To The Club</span>
          <span class="signup__header-message--2">Your Style Begins Here</span>
        </h2>
      <!-- <div class="signup__center"> -->

        

        <img src="@/assets/beard.jpg" alt="">
        <p class="legal">By clicking the Submit button, you agree to creating a free account, and to <span class="small-link">Terms of Service</span>  and <span class="small-link">Privacy Policy</span>.</p>
      <!-- </div> -->
    </div>
    
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
        this.formSubmitSuccess = true

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

@import url('https://fonts.googleapis.com/css2?family=Mr+De+Haviland&display=swap');

.signup {
  display: flex;
  height: 100vh;

.legal {
  padding: 0 2rem;
  font-size: 2rem;
}


  &__left {
    background-color: #f9f9f9;
    background-color: #fafafa;
    flex: 2;
    display: grid;
    place-items: center;
  }

  &__right {
    background-color: #fdac2c;
    flex: 1.5;
    // display: grid;
    // place-items: center;
    color: white;
    text-align: center;
    font-family: 'Mr De Haviland', cursive;
    position: relative;
  }

  &__center {
    width: 39rem;
  }

  &__header {
    margin-bottom: 2.6rem;
    font-size: 2.4rem;

    &--grey{
      color: #282426
    }

    &--white{
      color: #fff;
      font-size: 5rem;
      width: 100%;
      overflow: hidden;
      white-space: nowrap;
      word-spacing: 1rem;
      margin-top: 10rem;
      margin-bottom: 0;
    }

    &-message--1, &-message--2{
      display: inline-block;
      width: 100%;
    }

    &-message--1{
      margin-left: -100%;
    }
  }

  img {

    // width: 100%;
    width: 39rem;

    &:hover {
      animation: shake 0.8s linear infinite;    
      transform-origin: 50% 100%;
      cursor: pointer;
    }
  }
}


.form {

  &__group {
    margin-bottom: 2rem;
    position: relative;

    &--error {
      animation: wobble .6s ease-in-out forwards;
    }

    &--error .form__label {
      color: #f04124;
      transition: all .2s ease-in-out;
    }

    &--error .form__input {
      transition: all .2s ease-in-out;
      border-color: #f79483;
    }

    &--error + .form__errors {
      opacity: 1;
      transition: all .2s ease-in-out;

    }

    &--error .form__icon {
      color: salmon;
      transition: all .2s ease-in-out;
    }
  }

  &__icon {
    position: absolute;
    display: inline-block;
    right: 2rem;
    top: 4.3rem;
    font-size: 1.5rem;
    color: #ccc;
  }
  
  &__input {
    outline: none;
    width: 100%;
    background-color: inherit;
    border: 1px solid #ededed;
    // border: none;
    // border-bottom: 1px solid #ededed;
    padding: 1.3rem 1.3rem;
    font-size: 1.4rem;
    color: #505050;
    border-radius: 3px;
    font-family: inherit;
    transition: border .1s ease;
    box-sizing: border-box;

    &::placeholder {
      color: #ccc;
      font-family: inherit;
      font-weight: normal;
      font-size: 1.3rem;
    }
  }

  &__label {
    color: #4b6372;
    color: #999;
    margin-left: 0.875rem;
    font-weight: 300;
    font-size: 1.35rem;
  }

  &__error {
    line-height: 1.6;
    margin-left: 1.4rem;
    color: #f57f6c;
    font-size: 1.13rem;
    transition: all 2s ease-in-out;
  }

  &__submit {
    // width: 100%;
    letter-spacing: .5px;
    margin-top: .4rem;
    cursor: pointer;
    font-family: "Lato", sans-serif;
    font-size: 1.3rem;
    color: #fff;
    line-height: 1.4;
    padding: 0.7rem 2.2rem 0.8rem;
    background-color: #41B883;
    border: none;
    border-radius: 3.5px;
    outline: none;
    vertical-align: middle;
    transition: all .2s ease-in-out;
    border-bottom: 3px solid #266d4d;
    border-bottom: 3px solid darken($color: #fdac2c, $amount: 18);
    background-color: #fdac2c;

    &:hover {
      background: darken($color: #fdac2c, $amount: 11);
    }
  }

  &__errors {
    opacity: 0;
    margin-top: -1.7rem;
    margin-bottom: 1.2rem;
    min-height: 1.9rem;
  } 

}

.move-right {
  animation: moveOut 1.2s cubic-bezier(0.68, -0.6, 0.32, 1.6) forwards;
}

@keyframes moveOut {
  100%{
    margin-left: 0;
  }
}


@keyframes wobble {
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

  @keyframes shake {
  0%  { transform:     translate(2px, 1px) rotate(0deg) scale(1.03); }
  10% { transform:     translate(-1px, -2px) rotate(-2deg) scale(1); }
  20% { transform:     translate(-3px, 0px) rotate(3deg) scale(1); }
  30% { transform:     translate(0px, 2px) rotate(0deg) scale(1.03); }
  40% { transform:     translate(1px, -1px) rotate(1deg) scale(1); }
  50% { transform:     translate(-1px, 2px) rotate(-1deg) scale(1.04); }
  60% { transform:     translate(-3px, 1px) rotate(0deg) scale(1); }
  70% { transform:     translate(2px, 1px) rotate(-2deg) scale(1.03); }
  80% { transform:     translate(-1px, -1px) rotate(4deg) scale(1); }
  90% { transform:     translate(2px, 2px) rotate(0deg) scale(1.03); }
  100%{ transform:     translate(1px, -2px) rotate(-1deg) scale(1); }
 }
</style>









































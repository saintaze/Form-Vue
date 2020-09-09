<template>
  <div class="signup">
    <div class="signup__left">
      <div class="signup__center">
        <h2 class="signup-heading">Sign Up</h2>        
        <form class="form" @submit.prevent="formSubmit">

          <!-- First Name -->
          <div class="form__group" :class="{ 'form__group--error': $v.data.firstName.$error }">
            <label class="form__label">First Name</label>
            <input type="text" class="form__input" v-model.trim="data.firstName" @input="validateFirstName" placeholder="Enter First Name"/>
          </div>
          <div class="form__errors" >
            <div class="form__error" v-if="!$v.data.firstName.required">First Name is required.</div>
            <div class="form__error" v-if="!$v.data.firstName.alpha">First Name must only consist of letters.</div>
            <div class="form__error" v-if="!$v.data.firstName.minLength">First Name must have atleast {{$v.data.firstName.$params.minLength.min}} characters.</div>
          </div>

          <!-- Last Name -->
          <div class="form__group" :class="{ 'form__group--error': $v.data.lastName.$error }">
            <label class="form__label">Last Name</label>
            <input type="text" class="form__input" v-model.trim="data.lastName" @input="validateLastName" placeholder="Enter Last Name"/>
          </div>
          <div class="form__errors" >
            <div class="form__error" v-if="!$v.data.lastName.required">Last Name is required.</div>
            <div class="form__error" v-if="!$v.data.lastName.alpha">Last Name must only consist of letters.</div>
            <div class="form__error" v-if="!$v.data.lastName.minLength">Last Name must have atleast {{$v.data.lastName.$params.minLength.min}} characters.</div>
          </div>
          
          <!-- Email -->
          <div class="form__group" :class="{ 'form__group--error': $v.data.email.$error }">
            <label class="form__label">Email</label>
            <input type="text" class="form__input" v-model.trim="data.email" @input="validateEmail" placeholder="Enter Email"/>
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
            <input type="password" class="form__input" v-model.trim="data.password" @input="validatePassword" placeholder="Enter Password"/>
            <i class="form__icon fas fa-eye form__icon"></i>
          </div>
          <div class="form__errors" >
            <div class="form__error" v-if="!$v.data.password.required">Password is required</div>
            <div class="form__error" v-if="!$v.data.password.isPassword">Password must have atleast 8 characters, one uppercase, one lowercase, one special and one number. </div>
          </div>

          <input class="form__submit" type="submit" >

        </form>
      </div>
    </div>

    <div class="signup__right">

      <h2 class="welcome-heading ">
        <span :class="{'move-right': formSubmitSuccess}" class="welcome-heading__message--1">Welcome To The Club</span>
        <span class="welcome-heading__message--2">Your Style Begins Here</span>
      </h2>

      <img class="mustache-img" src="@/assets/beard.jpg" alt="mustache image">

      <div class="fine-print">
        <p :class="{'move-up1': formSubmitSuccess}" class="fine-print__message--1">By clicking the Submit button, you agree to creating a free account, and to Terms of Service and Privacy Policy.</p>
        <p :class="{'move-up2': formSubmitSuccess}" class="fine-print__message--2">Your days of bad looks are over. Now people would be enchanted by you like never before</p>
      </div>

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
      firstName: { required, alpha, minLength: minLength(3) },
      lastName: { required, alpha, minLength: minLength(3) },
      password: { 
        required, 
        isPassword(value){
          if(value === '') return true;
          return /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/.test(value);
        }
      },
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
            return res.data.data.status === 'OK';
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
        const payload = {
          "campaignUuid": "46aa3270-d2ee-11ea-a9f0-e9a68ccff42a",
          data: this.data
        }
        const res = await axios.post(SIGNUP_URL, payload);
        this.formSubmitSuccess = true;
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


// Variables

$color-orange: #fdac2c;
$color-white-smoke: #fafafa;
$color-grey-light-1: #ccc;
$color-grey-light-2: #ededed;
$color-red-dark-1: #f04124;
$color-red-light-1: salmon;
$color-red-light-2: #f79483;
$color-red-light-3: #f57f6c;
$color-grey-dark-1: #282426;
$color-grey-dark-2: #505050;
$color-grey-dark-3: #4b6372;


// Animation classes

.move-up1{
  animation: moveUp1 2s 1.4s forwards;
}

.move-up2{
  animation: moveUp2 1.4s 2.5s forwards;
}

.move-right {
  animation: moveRight 1.2s cubic-bezier(0.68, -0.6, 0.32, 1.6) forwards;
}


// Signup Wrapper

.signup {
  display: flex;
  min-height: 100vh;
  
  @media(max-width: 910px){
    flex-direction: column;
  }

  &__left {
    background-color: $color-white-smoke;
    flex: 2;
    width: 60%;
    display: grid;
    place-items: center;
    padding: 3rem;

    @media(max-width: 910px){
      padding: 4rem;
      order: 1;
    }
  }

  &__right {
    background-color: $color-orange;
    flex: 1.5;
    width: 40%;
    text-align: center;
    font-family: 'Mr De Haviland', cursive;
    padding: 10rem 0;
  }

  &__left, &__right {
    @media(max-width: 910px){
      width: 100%;
    }
  }

  &__center {
    width: 39rem;
  }
}


// Mustache Image 

.mustache-img {
  width: 39rem;

  &:hover {
    animation: shake 0.8s linear infinite;    
    transform-origin: 50% 100%;
    cursor: pointer;
  }
}


// Signup Heading 

.signup-heading {
  margin-bottom: 2.6rem;
  font-size: 2.4rem;
  color: $color-grey-dark-1;
}


// Welcome Heading

.welcome-heading {
  color: $color-white-smoke;
  font-size: 5rem;
  width: 100%;
  overflow: hidden;
  white-space: nowrap;
  word-spacing: 1rem;
  margin-bottom: 0.3rem;

  &__message--1, &__message--2{
    display: inline-block;
    width: 100%;
  }

  &__message--1 {
    margin-left: -100%;
  }
}


// Fine Print

.fine-print {
  font-size: 2rem;
  width: 100%;
  color: $color-white-smoke;
  position: relative;
  height: 3rem;

  &__message--1, &__message--2 {
    position: absolute;
    width: 100%;
    padding: 0 3rem;    
  }

  &__message--1{
    top: 1rem;
  }

  &__message--2{
    opacity: 0;
  }
}


// Form

.form {
  &__group {
    margin-bottom: 2rem;
    position: relative;

    &--error {
      animation: wobble .6s ease-in-out forwards;
    }

    &--error .form__label {
      color: $color-red-dark-1;
      transition: all .2s ease-in-out;
    }

    &--error .form__input {
      border-color: $color-red-light-2;
      transition: all .2s ease-in-out;
    }

    &--error .form__icon {
      color: $color-red-light-1;
      transition: all .2s ease-in-out;
    }

    &--error + .form__errors {
      opacity: 1;
      transition: all .2s ease-in-out;
    }
  }

  &__label {
    color: $color-grey-dark-3;
    margin-left: 0.875rem;
    font-weight: 300;
    font-size: 1.35rem;
  }
  
  &__input {
    outline: none;
    width: 100%;
    background-color: inherit;
    border: 1px solid $color-grey-light-2;
    padding: 1.3rem 1.3rem;
    font-size: 1.4rem;
    color: $color-grey-dark-2;
    border-radius: 3px;
    font-family: inherit;
    transition: border .2s ease-in-out;
    box-sizing: border-box;

    &::placeholder {
      color: $color-grey-light-1;
      font-family: inherit;
      font-size: 1.3rem;
    }
  }

  &__icon {
    position: absolute;
    display: inline-block;
    right: 2rem;
    top: 4.3rem;
    font-size: 1.5rem;
    color:$color-grey-light-1;
  }

  &__errors {
    opacity: 0;
    margin-top: -1.7rem;
    min-height: 3.6rem;
  } 

  &__error {
    line-height: 1.4;
    margin-left: 1.4rem;
    color: $color-red-light-3;
    font-size: 1.13rem;
    transition: all 2s ease-in-out;
  }

  &__submit {
    letter-spacing: .5px;
    margin-top: .5rem;
    cursor: pointer;
    font-size: 1.3rem;
    color: $color-white-smoke;
    line-height: 1.4;
    padding: 0.7rem 2.2rem 0.8rem;
    border: none;
    border-radius: 3.5px;
    outline: none;
    transition: all .2s ease-in-out;
    border-bottom: 3px solid darken($color: $color-orange, $amount: 18);
    background-color: $color-orange;

    &:hover {
      background: darken($color: $color-orange, $amount: 11);
    }
  }
}


// Animations

@keyframes moveRight {
  100%{
    margin-left: 0;
  }
}

@keyframes moveUp1 {
  100%{
    transform: translateY(-20px);
    opacity: 0;
  }
}

@keyframes moveUp2 {
  0%{
    opacity: 0;
    transform: translateY(1rem);
  }

  100%{
    transform: translateY(0);
    opacity: 1;
  }
}

@keyframes wobble {
  0% { transform: translateX(0); }
  15% { transform: translateX(0.375rem); }
  30% { transform: translateX(-0.375rem); }
  45% { transform: translateX(0.375rem); }
  60% { transform: translateX(-0.375rem); }
  75% { transform: translateX(0.375rem); }
  90% { transform: translateX(-0.375rem); }
  100% { transform: translateX(0); } 
}

@keyframes shake {
  0%  { transform: translate(2px, 1px) rotate(0deg) scale(1.03); }
  10% { transform: translate(-1px, -2px) rotate(-2deg) scale(1); }
  20% { transform: translate(-3px, 0px) rotate(3deg) scale(1); }
  30% { transform: translate(0px, 2px) rotate(0deg) scale(1.03); }
  40% { transform: translate(1px, -1px) rotate(1deg) scale(1); }
  50% { transform: translate(-1px, 2px) rotate(-1deg) scale(1.04); }
  60% { transform: translate(-3px, 1px) rotate(0deg) scale(1); }
  70% { transform: translate(2px, 1px) rotate(-2deg) scale(1.03); }
  80% { transform: translate(-1px, -1px) rotate(4deg) scale(1); }
  90% { transform: translate(2px, 2px) rotate(0deg) scale(1.03); }
  100%{ transform: translate(1px, -2px) rotate(-1deg) scale(1); }
 }
</style>









































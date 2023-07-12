<template>
  <div class="container">

    <div class="header">
      <div class="header-description">
        <img class="logo" src="../Imagenes/logo.png" alt="logo">
        <h3 class="header-title">Register to ToDo App</h3>
        <p class="header-subtitle">Start organizing your tasks!</p>
      </div>
    </div>

    <form @submit.prevent="signUp" class="form-sign-in">
      <div class="form">
        <div class="form-input">
          <label class="input-field-label">E-mail</label>
          <input
            type="email"
            class="input-field"
            placeholder="example@gmail.com"
            id="email"
            v-model="email"
            required
          />
        </div>
        <div class="form-input">
          <label class="input-field-label">Password</label>
          <input
            type="password"
            class="input-field"
            placeholder="**********"
            id="password"
            v-model="password"
            required
          />
        </div>
        <div class="form-input">
          <label class="input-field-label">Confirm password</label>
          <input
            type="password"
            class="input-field"
            placeholder="**********"
            id="confirmPassword"
            v-model="confirmPassword"
            required
          />
        </div>
        <button class="button" type="submit">Sign Up</button>
        <p>
          Have an account?
          <PersonalRouter
            :route="route"
            :buttonText="buttonText"
            class="sign-up-link"
          />
        </p>
      </div>
    </form>

    <div v-show="errorMsg">{{errorMsg}}</div>
  </div>
</template>

<script setup>
import { ref, reactive, watch } from "vue";
import PersonalRouter from "./PersonalRouter.vue";
import { supabase } from "../supabase";
import { useRouter } from "vue-router";
import { useUserStore } from "../stores/user";
import { storeToRefs } from "pinia";

// Route Variables
const route = "/auth/login";
const buttonText = "Sign In";

// Input Fields
const email = ref("");
const password = ref("");
const confirmPassword = ref("");

// Error Message
const errorMsg = ref("");

// Router to push user once SignedUp to Log In
const redirect = useRouter();

// Arrow function to SignUp user to supaBase with a timeOut() method for showing the error
const signUp = async () => {
  if (password.value === confirmPassword.value) {
    try {
      // calls the user store and send the users info to backend to logIn
      await useUserStore().signUp(email.value, password.value);
      // redirects user to the homeView
      redirect.push({ path: "/auth/login" });
    } catch (error) {
      // displays error message
      errorMsg.value = error.message;
      // hides error message
      setTimeout(() => {
        errorMsg.value = null;
      }, 5000);
    }
    return;
  }
  errorMsg.value = "Los datos introducidos, no son corretos.";
};
</script>

<style>
p {
  text-align: center;
  margin-top: 10px;
  color: white;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.sign-up-link{
  color: white;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}
 .container{
  height: 100vh;
  width: 100%;
  background-size: cover;
  background-image: url('../Imagenes/background.jpeg');
  background-repeat: no-repeat; 
  background-position: center;
}

.header-title {
  color:gold;
  text-align: center;
  margin-bottom: 20px;
  font-size: 45px;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.header-subtitle {
  color: white;
  text-align: center;
  margin-bottom: 9px;
  font-size: 20px;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.logo {
  border-radius: 10px;
  margin-top: 60px;
  margin-bottom:15px ;
  height: 125px;
  width: 125px;
}

.form-sign-in {
  padding: 10px 10px 10px 10px;
}
.form-input {
  margin-bottom: 5px;
}
.input-field-label {
  font-size: 15px;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
  color: white
}

.input-field {
  width: 100%;
  padding: 10px;
  border: 1px solid;
  border-radius: 5px;
}

.button {
  padding: 13px 18px;
  background-color: gold;
  color: black;
  border: none;
  border-radius: 5px;
  width: 80%;
}



</style>

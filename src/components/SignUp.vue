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
          <input
            type="email"
            class="input-field"
            placeholder="example@gmail.com"
            id="email"
            v-model="email"
            required
          />
          <input
            type="password"
            class="input-field"
            placeholder="Enter password"
            id="password"
            v-model="password"
            required
          />
          <input
            type="password"
            class="input-field"
            placeholder="Confirm Password"
            id="confirmPassword"
            v-model="confirmPassword"
            required
          />
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
  if((password.value).length>=6){
    if (password.value === confirmPassword.value) {
      try {
        // calls the user store and send the users info to backend to logIn
        await useUserStore().signUp(email.value, password.value);
        alert( "Revisa tu correo electrónico para validar tu cuenta.");
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
    }else {
      errorMsg.value = "Los datos introducidos, no son corretos.";
      alert( errorMsg.value);
      
    }
  }else{
    alert( "La contraseña es demasiado corta, debe tener 6 o más carácteres.");
  }
  
};
</script>

<style scoped>
p {
  text-align: center;
  margin-top: 10px;
  color: white;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}
.form-input{
  width: 100%;
  padding: 15px;
  border: 1px solid white;
  border-radius: 5px;
}
.sign-up-link{
  color: white;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.form-sign-in {
   padding:30px 30px 30px 30px; 
   width:70%;
}
 .container{
  height:100vh;
  width: 100%;
  background-size: cover;
  background-image: url('../Imagenes/background.jpeg');
  background-repeat: no-repeat; 
  background-position: center;
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.header-title {
  color:gold;
  text-align: center;
  margin-bottom: 30px;
  font-size: 45px;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.header-subtitle {
  color: white;
  text-align: center;
  margin-bottom: 10px;
  font-size: 18px;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.logo {
  border-radius: 15px;
  margin-top: 60px;
  margin-bottom:20px; 
  height: 120px;
  width: 120px;
  opacity: 1;
}

.form-sign-in {
  padding: 40px 40px 40px 40px;
}
.form-input {
  margin-bottom: 8px;
  width: 80%;
}
.input-field-label {
  font-size: 15px;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
  color: white
}

.input-field {
  width: 80%;
  padding-top: 15px;
  padding-bottom: 15px;
  border: 1px solid white;
  border-radius: 5px;
  margin-bottom:15px;
}

.button {
  padding-top: 15px;
  padding-bottom: 15px;
  background-color: gold;
  color: black;
  border: none;
  border-radius: 7px;
  cursor: pointer;
  width: 80%;
}



</style>

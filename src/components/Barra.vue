<template>
  <nav class="menu">
    <!-- <PersonalRouter :route="route" :buttonText="buttonText" class="logo-link"/> -->
    <div class="logoImg">
    <img class="barraImg" src="../Imagenes/logo.png" alt="logo"/>
    </div>

    <router-link to="/" class="linkBarra"> Home </router-link>

    <router-link to="/account" class="linkBarra">Your Account</router-link>

    <router-link to="/" class="linkBarra">Your Tasks</router-link>


    <div class="">
      <ul>
        <li class="log-out-welcome">
          <p class="linkBarra">Welcome,  
            <span class="email">{{ getEmailPrefix(getUser.email) }}</span></p>
        </li>
        <li>
          <button @click="signOut" class="logOut">Log Out</button>
        </li>
      </ul>
    </div>
  </nav>
</template>

<script setup>
// import PersonalRouter from "./PersonalRouter.vue";
import { useUserStore } from "../stores/user";
import { computed } from "vue";
import { useRouter } from "vue-router";
import { ref } from 'vue';
import { supabase } from "../supabase";

const user = supabase.auth.user();

//constant to save a variable that will hold the use router method
const route = "/";
const buttonText = "Todo app";

// constant to save a variable that will get the user from store with a computed function imported from vue
// const getUser = computed(() => useUserStore().user);
const getUser = useUserStore().user;
console.log(useUserStore().user);

// constant that calls user email from the useUSerStore
const userEmail = getUser.email;

const userStore = useUserStore();
const router = useRouter();

const getUserEmail = () => {
  const user = getUser.value;
  if (user) {
    userEmail.value = user.email;
  }
};
getUserEmail();

// async function that calls the signOut method from the useUserStore and pushes the user back to the Auth view.
const redirect = useRouter();

const signOut = async () => {
  try{
    // call the user store and send the users info to backend to signOut
    // then redirect user to the homeView

    await useUserStore().signOut();
    redirect.push({ path: "/auth/login" });

  } catch (error) {}
};

const getEmailPrefix = (email) => {
  const atIndex = email.indexOf('@');
  if (atIndex !== -1) {
    return email.slice(0, atIndex);
  }
  return email;
};
</script>

<style scoped>

.barraImg {
  width: 80px;
  background-color: transparent;
}

nav {
  background-color: rgb(51, 51, 132);
  display: flex;
  width: 100%;
  justify-content: space-around;
  align-items: center;
  

}

nav ul {
  list-style: none;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: transparent;

}

.linkBarra{
  color: gold;
  text-decoration: none;
  font-size: 3vh;
  text-decoration: none;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
  background-color: transparent;
  
}

.email{
  color: gold;
  background-color: transparent;

}


.logOut {
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
  height: 45px;
  width: 100px;
  align-content: center;
  padding: 10px 15px;
  background-color: gold;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  
}
</style>

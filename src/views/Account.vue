<template>
  
  <Barra />
  <br>
  <br>
  <br>
  <br>
  <br>
  <br>

  <div class="container">
    <div class="profile">
  <h1>Name: {{username}}</h1>
  <img :src="avatar_url ? avatar_url : 'https://cdn.pixabay.com/photo/2015/10/05/22/37/blank-profile-picture-973460__480.png'" alt="Profile picture">
  </div>
  <div>
    <h2>Nombre de usuario: {{ username }}</h2>
    <h2>Nombre completo: {{ name }}</h2>
    <h2>Website: {{ website }}</h2>
  </div>
  <button class="avatarProfile" @click.prevent="editProfileButton">Edit your profile</button>


  <form class="form-widget" @submit.prevent="updateProfile">
    </form>
    <Footer />
  </div>

</template>

<script setup>
  import { supabase } from '../supabase';
  import { onMounted, ref, toRefs } from 'vue';
  import { useRouter } from "vue-router";

  import { useUserStore } from "../stores/user";
  import Barra from '../components/Barra.vue';
  import Footer from "../components/Footer.vue";


  const userStore = useUserStore();

  const loading = ref(false);
  const username = ref(null);
  const website = ref(null);
  const avatar_url = ref(null);
  const name = ref(null);
  const email = ref(null);
  const redirect = useRouter();


  onMounted(() => {
    getProfile();
  });

  async function getProfile() {
    await userStore.fetchUser();
    username.value = userStore.profile.username;
    avatar_url.value = userStore.profile.avatar_url;
    website.value = userStore.profile.website;
    name.value = userStore.profile.full_name;
    email.value = userStore.profile.email;
  }

  async function signOut() {
    try {
      loading.value = true
      let { error } = await supabase.auth.signOut()
      if (error) throw error
    } catch (error) {
      alert(error.message)
    } finally {
      loading.value = false
    }
  }

  const editProfileButton = () => {
  redirect.push({ path: "/editprofile" });
};

</script>

<style scoped>
img {
  width: 200px;
  border-radius: 50%;
}

.avatarProfile {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  margin-bottom: 6vh;
}

.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 4vw;
  height: 40vh;
  width: 100%;
  padding-top: 30vh;
  background-color: rgb(20, 123, 213);

  position: absolute;
  top: 0;
  text-align: center;
  color: white;
}

.container h1 {
  font-size: 20px;
  color: white;
  text-align: center;
  margin-bottom: 10px;
  font-size: 18px;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.container h2 {
  font-size: 20px;
  color: white;
  text-align: center;
  margin-bottom: 10px;
  font-size: 18px;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}
</style>
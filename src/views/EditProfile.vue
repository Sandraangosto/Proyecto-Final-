<template>
    <Barra />
    <div class="edit">
    <form class="edit-profile">
      
      <label for="name" class="input-field">Introduce tu nombre</label>
      <input class="input" type="text" autocomplete="off" name="name" v-model="name" placeholder="Your Name"/>
  
     
      <label for="username" class="input-field">Introduce tu Nombre de Usuario</label>
      <input class="input" type="text" autocomplete="off" name="username" v-model="username" placeholder="Your UserName"/>
  
      
      <label for="website" class="input-field">Tu sitio web:</label>
      <input class="input" type="text" autocomplete="off" name="website" v-model="website" placeholder="Your WebSite"/>
  
      
      <input @change="uploadAvatar" type="file" accept=".jpg, .jpeg, png, .gif"/>
      <button class="button" @click.prevent="editProfiles">Guardar cambios</button>
    </form>
    <Footer />

  </div>
  </template>

<script setup>
import { supabase } from "../supabase";
import { onMounted, ref, toRefs } from "vue";
import { useUserStore } from "../stores/user";
import Barra from "../components/Barra.vue";
import { useRouter } from "vue-router";
import Footer from "../components/Footer.vue";


const userStore = useUserStore();

const redirect = useRouter();
const loading = ref(false);
const username = ref(null);
const website = ref(null);
const avatar_url = ref(null);
const name = ref(null);
onMounted(() => {
  getProfile();
});

async function getProfile() {
  await userStore.fetchUser();
  username.value = userStore.profile.username==undefined ? "" : userStore.profile.username;
  avatar_url.value = userStore.profile.avatar_url==undefined ? "" : userStore.profile.avatar_url;
  website.value = userStore.profile.website==undefined ? "" : userStore.profile.website;
  name.value = userStore.profile.full_name==undefined ? "" : userStore.profile.full_name;
}

async function editProfiles() {
  if (
    website.value.length === 0 ||
    username.value.length === 0 ||
    name.value.length === 0
  ) {
    alert("The information can not be empty");
  } else {
    await userStore.fetchUser();
    await userStore.editProfile(
      username.value,
      website.value,
      name.value,
      avatar_url.value
    );
    redirect.push({ path: "/account" });
  }
}

const prop = defineProps(["path", "size"]);
const { path, size } = toRefs(prop);
const emit = defineEmits(["upload", "update:path"]);
const uploading = ref(false);
const src = ref("");
const files = ref();


const uploadAvatar = async (evt) => {
  files.value = evt.target.files;
  try {
    uploading.value = true;
    if (!files.value || files.value.length === 0) {
      throw new Error("You must select an image to upload.");
    }
    const file = files.value[0];
    const fileExt = file.name.split(".").pop();
    const filePath = `${Math.random()}.${fileExt}`;
    let { error: uploadError } = await supabase.storage
      .from("avatars")
      .upload(filePath, file, { upsert: false });


    avatar_url.value =
      "https://znfqtvlnlyjlzfoyeloj.supabase.co/storage/v1/object/public/avatars/" +
      filePath;
    if (uploadError) throw uploadError;
    emit("update:path", filePath);
    emit("upload");
  } catch (error) {
    alert(error.message);
  } finally {
    uploading.value = false;
  }
};
</script>

<style scoped>

.button {
  padding: 10px 0px;
  background-color: gold;
  color: black;
  border: none;
  border-radius: 7px;
  cursor: pointer;
  margin-top: 10px;
}

.edit{
  height:70vh;
  width: 100%;
  background-image: url("../Imagenes/background.jpeg");
  background-size: cover;
  background-repeat: no-repeat;
  padding-top:30vh;
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  align-items: center;
}

.edit-profile {
  color: gold;
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 50%;
  text-align: center;
  font-size: 20px;
  
}

.input-field {
  
  padding-top: 15px;
  padding-bottom: 15px;
  border: 1px solid white;
  border-radius: 5px;
  margin-bottom:15px;
}

.input {
  padding-top: 15px;
  padding-bottom: 15px;
  border: 1px solid white;
  border-radius: 5px;
  margin-bottom:15px;
}
 @media screen {
    .input-field{
      border: none;
    }
    .button {
     background-color: gold;
     color: black;
     border: none;
     border-radius: 7px;
     cursor: pointer;
}
 }
</style>
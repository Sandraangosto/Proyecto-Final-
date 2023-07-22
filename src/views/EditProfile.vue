<template>
    <Barra />
    <br>
    <br>
    <br>
    <br>
    <br>
    <br>
    <br>
    <br> 
    <div class="edit">
    <form class="edit-profile">
      
      <label for="name" class="input-field">Introduce tu nombre</label>
      <input type="text" autocomplete="off" name="name" v-model="name" placeholder="Your Name"/>
  
     
      <label for="username" class="input-field">Introduce tu Nombre de Usuario</label>
      <input type="text" autocomplete="off" name="username" v-model="username" placeholder="Your UserName"/>
  
      
      <label for="website" class="input-field">Tu sitio web:</label>
      <input type="text" autocomplete="off" name="website" v-model="website" placeholder="Your WebSite"/>
  
      
      <input @change="uploadAvatar" type="file" accept=".jpg, .jpeg, png, .gif"/>
      <button class="button" @click.prevent="editProfile">Guardar cambios</button>
    </form>
  </div>
  </template>

<script setup>
import { supabase } from "../supabase";
import { onMounted, ref, toRefs } from "vue";
import { useUserStore } from "../stores/user";
import Barra from "../components/Barra.vue";
import { useRouter } from "vue-router";

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
  username.value = userStore.profile.username;
  avatar_url.value = userStore.profile.avatar_url;
  website.value = userStore.profile.website;
  name.value = userStore.profile.name;
}

async function editProfile() {
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
      avatar_url.value,
      name.value
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
      "https://hzfkzntspuuexihwuuyx.supabase.co" +
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
  padding: 10px 30px;
  background-color: gold;
  color: black;
  border: none;
  border-radius: 7px;
  cursor: pointer;
  width: 80%;
}

.edit{
  width: 100%;
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}
.edit{
  background-color: rgb(20, 123, 213);

}
.edit-profile {
  color: gold;
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100%;
  text-align: center;
  
}

.input-field {
  width: 100%;
  padding: 15px;
  border: 1px solid white;
  border-radius: 5px;
}

</style>
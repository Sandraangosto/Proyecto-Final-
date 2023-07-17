<template>
    <div class="addTask">
        <h1 class="titulo">Add a new Task</h1>
        <p class="subtitulo">Keep Your Tasks Here! </p>
        <div>
            <p><strong class="date">Today is {{ formattDate }}</strong></p>
        </div>
    <div v-if="showErrorMessage">
        <p class="error-text">{{ errorMessage }}</p>
    </div>
    <div>
        <div class="input-field">
            <input type="text" placeholder="Add a Task Title - Listen to Kendrick Lamar" v-model="name">
        </div>
        <div class="input-field">
            <input type="text" placeholder="Add a Task Description - Look up Kendrick Lamar's FEAR album on spotify and listen to the whole album." v-model="description">
        </div>
        <button @click="addTask" class="button">Add</button>
    </div>
    </div>

    
</template>

<script setup>
import { ref,onMounted } from "vue";
import { useTaskStore } from "../stores/task" 
import { supabase } from "../supabase";


const taskStore = useTaskStore();

// variables para los valors de los inputs
const name = ref('');
const description = ref('');

// constant to save a variable that holds an initial false boolean value for the errorMessage container that is conditionally displayed depending if the input field is empty
const showErrorMessage = ref(false);

// const constant to save a variable that holds the value of the error message
const errorMessage = ref(null);
const emit = defineEmits(["addTitle"]);


// Arrow function para crear tareas.
const addTask = () => {
if(name.value.length === 0 || description.value.length === 0){
    // Primero comprobamos que ningún campo del input esté vacío y lanzamos el error con un timeout para informar al user.

    showErrorMessage.value = true;
    errorMessage.value = 'The task title or description is empty';
    setTimeout(() => {
    showErrorMessage.value = false;
    }, 5000);

} else {
    // Aquí mandamos los valores a la store para crear la nueva Task. Esta parte de la función tenéis que refactorizarla para que funcione con emit y el addTask del store se llame desde Home.vue.

    taskStore.addTask(name.value, description.value);
    name.value = "";
    description.value = "";

    emit("addTitle");

    location.reload();


}
};

const currentDate = ref(new Date());

const updateCurrentDate = () => {
  currentDate.value = new Date();
};

onMounted(() => {
  updateCurrentDate();
  setInterval(updateCurrentDate, 24 * 60 * 60 * 1000); 
});

const formattedDate = ref('');

onMounted(() => {
  const options = { day: 'numeric', month: 'long', year: 'numeric' };
  const formatter = new Intl.DateTimeFormat('en', options);
  formattedDate.value = formatter.format(currentDate.value);
});

</script>

<script>
export default {
  methods: {
    scrollToBottom() {
      window.scrollTo({
        top: document.body.scrollHeight,
        behavior: "smooth"
      });
    }
  }
}

</script>
<style scoped>

.addTask{
  background-image: url("../Imagenes/background.jpeg");
  height: 100vh;
  width: 100%;
  text-align: center;
  position: relative;
  background-repeat: no-repeat;
  background-size: cover;
}


.titulo{
  color:gold;
  text-align: center;
  margin-bottom: 20px;
  font-size: 60px;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.subtitulo{
  color: gold;
  text-align: center;
  margin-top: 30px;
  margin-bottom: 10px;
  font-size: 20px;
  font-family:Copperplate, Copperplate Gothic Light, fantasy;
}

.date{
  color:gold;
  font-family: Copperplate, Copperplate Gothic Light, fantasy;

}

.addTask {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 60vh;
}

.input-field {
  margin-bottom: 8px;
}

.input-field input {
  width: 600px;
  height: 50px;
  border-radius: 4px;
  padding: 8px 8px;
}

.input-field textarea{
padding: 8px 8px;
height: 55px;
width: 600px;
border-radius: 4px;
}

.button {
  width:500px;
  padding: 10px 20px;
  background-color:#a504b7;
  color: gold;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}






</style>
  
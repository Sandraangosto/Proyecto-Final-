<template>
<div class="task-item-fondo">
    <div class="card">
      <div class="card2">
        <h3 :class="props.task.is_complete ? 'done' : 'pending'">
          {{ task.title }}
        </h3>
        <p :class="props.task.is_complete ? 'done' : 'pending'">
          {{ task.description }}
        </p>
        <div class="botones">
          <button @click="completedTask" class="boton1">
            <img src="../Imagenes/tarea-completada.png" alt="tarea-completada"/>
           
          </button>
        <!-- boton de borrar tareas -->
        <button @click="showModalToggle" class="boton3">
            <img src="../Imagenes/borrar.png" alt="Borrar"/>
          
          </button>

          <!-- boton para editar tareas -->
          <button @click="inputToggle" class="boton2">
           <img src="../Imagenes/editar.png" alt="Edit" /> 
          </button>
          <div v-if="showInput">
            <div class="container-edit">
              <div class="textInputWrapper">
                <input
                  v-model="newTitle" placeholder="Introduce un nuevo titulo" type="text" class="textInput"/>
              </div>
              
            <div class="textInputWrapper">
                <input v-model="newDescription" placeholder="Introduce un nuevo descripción" type="text" class="textInput"/>
              </div>
            <button @click="sendData" class="boton8">
              <span class="boton8-content">Actualizar tarea</span>
            </button>
          </div>
        </div>
        </div>
      </div>
    </div>
  </div>
  <div class="aviso-modal" v-if="showModal">
    <div class="modal">
      <img class="warning" src="../Imagenes/icono-error.png" alt="alert"/>

      <div class="columns-modal">
        <h2>Seguro que quieres <br/> borrar esta tarea?
        </h2>
        <div class="buttons-modal">
          <button @click="deleteTask" class="boton8">
            <span class="boton8-content">Si, quiero borrar! </span>
          </button>
        </div>

        <button @click="showModalToggle" class="boton8">
          <span class="boton8-content">No, quiero cancelar </span>
        </button>
      </div>
    </div>
  </div>


</template>

<script setup>
import { ref, onUpdated, watch } from 'vue';
import { useTaskStore } from '../stores/task';
import { supabase } from '../supabase';

const taskStore = useTaskStore();

const props = defineProps({
    task: Object,
});

const emit = defineEmits(["childComplete", "editChild"]);

const name = ref("");
const description = ref("");

const showInput = ref(false);
const newTitle = ref("");
const newDescription = ref("");

function inputToggle() {
  showInput.value = !showInput.value;
};


//funcion para completar tareas
const completedTask = () => {
  emit("childComplete", props.task);
  location.reload();

};

// Función para borrar la tarea a través de la store. El problema que tendremos aquí (y en NewTask.vue) es que cuando modifiquemos la base de datos los cambios no se verán reflejados en el v-for de Home.vue porque no estamos modificando la variable tasks guardada en Home. Usad el emit para cambiar esto y evitar ningún page refresh.
const deleteTask = async() => {
    await taskStore.deleteTask(props.task.id);
    emit("updateTask");
    location.reload();


};

const showModal = ref(false);
const showModalToggle = () => {
  showModal.value = !showModal.value;

};

const showErrorMess = ref(false);
const errorMess = ref(null);

const inputUpdate = ref(false);

//funcion para editar las tareas
const sendData = async () => {
  if (newTitle.value.length < 0 || newDescription.value.length < 0) {
    showErrorMess.value = true;
    errorMess.value =
      "El titulo o la descripción de la tarea son muy cortos.";
    setTimeout(() => {
      showErrorMess.value = false;
    }, 5000);
    location.reload();
  } else {
    taskStore.editTask(newTitle.value, newDescription.value, props.task.id);
    showInput.value = !showInput.value;
    emit("editChild", newTaskEdited);
    location.reload();

  }
};

const toggleComplete = () => {
  props.task.is_complete = !props.task.is_complete;
  taskStore.completeTask(props.task.id, props.task.is_complete);
  location.reload();

};
</script>

<style scoped>

.task-item-fondo{
  /*background-color: rgb(20, 123, 213);*/
  background-color: transparent;
  width: 25%;
  padding-left:5px;
  padding-bottom: 5px;

}
.taskComplete {
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 9999;
  background: linear-gradient( rgb(253, 0, 0), #e55454);
  width: 60vw;
  height: 60vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 5vw;
  color: black;
  animation: fade-in 0.5s forwards, modal-grow 0.5s forwards;
}
.aviso-modal {
  z-index: 1;
  background-color: #00000080;
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
}
.boton8 {
  position: relative;
  overflow: hidden;
  height: 3rem;
  padding: 0 2rem;
  border-radius: 1.5rem;
  background: #3d3a4e;
  background-size: 400%;
  color: #fff;
  margin-right: 10px;
}

.textInputWrapper {
  position: relative;
  width: 180px;
  margin: 12px 5px;
  --accent-color: #fbf8f5;
}
.boton3{
  width: 10%;
  height:10%;
  margin-right: 10px;
  background: none;
  border: none;

}
.boton1, .boton2{
  width: 10%;
  height: 10%;
  margin-right: 10px;
  background: none;
  border: none;

}
.card{
  width: 100%;
  height: 100%;
  border: 1px solid black;
  background-color: white;
  text-align: center;
  justify-content: space-between;
  border-radius: 5px;

}
</style>

<!--
**Hints**
1. ref() or reactive() can be used here to store the following, think if you want to store them either individually or
like an object, up to you.

2. Data properties need here are the following: a boolean to store a false**Important variable, a string to store an error,
a string to store the value of the task that the user can edit, an initial false boolean to hide the inputFIeld used to edit
the new task detail or details[this is in reference of the task title and the task description].

3. Store the custom emit events that will be used to call the functions of the homeView for editing, deleting and toggling the
status[completed, not complted] of the taskItem.

4. Function to handle the error with the data properties used to control the error and the the boolean controlling the boolean
empty variable.

5. Function to handle the edit dialogue where the inputField is displayed and the string used to store the value of the
inputField will be used here to save the value as a prop on this function.

6. Function to emmit a custom event emit() that takes 2 parameters a name for the custom event and the value that will be
send via the prop to the parent component. This function can control the toggle completion of the task on the homeview.

7. Function to edit the task information that you decided that the user can edit. This function's body takes in a conditional
that first checks if the value of the task [either title and description or just title] is empty which if true it runs the
function used to handle the error on hint4. Else, the conditional sets the first mentioned boolean data property on hint2
back to its inital boolean value to hide the error message and repeat the same for the data property mentioned 4th on hint2;
a constant that stores an object that holds the oldValue from the prop item and the value of the task coming from the data
property mentioned 3rd on hint2; a custom event emit() that takes 2 parameters a name for the custom event and the value
from the object used on this part of the conditional and lastly this part of the conditional sets the value of input field
to an empty string to clear it from the ui.

8. Function to emmit a custom event emit() that takes 2 parameters a name for the custom event and the value that will be
send via the prop to the parent component. This function can control the removal of  the task on the homeview.
-->

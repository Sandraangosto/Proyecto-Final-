<template>
<div class="task-item-fondo">
    <div class="card">
      <div class="card2">
        <!-- los case hacen relacion a el boton de completar -->
        <h3 :class="props.task.is_complete ? 'done' : 'pending'">
          {{ task.title }}
        </h3>
        <p :class="props.task.is_complete ? 'done' : 'pending'">
          {{ task.description }}
        </p>
        <!-- <button @click=>Marcar como completada</button> -->
        <div class="buttons">
          <button @click="completedTask" class="botton1">
           <!-- <img
              src="../../assets/vecteezy_check-mark-icon-sign-symbol-design_10141449_805.png"
              alt="Completar"
            />
            -->
            <!-- {{ task.is_complete ? "No completada 😐" : "Completada!" }} -->
          </button>
        <!-- boton de borrar tareas -->
        <button @click="showModalToggle" class="botton3">
            <!--<img
              src="../../assets/TaskItem_Botons/papelera_borrar.png"
              alt="Borrar"
            />
            -->
          </button>

          <!-- boton para editar tareas -->
          <button @click="inputToggle" class="botton2">
           <!-- <img src="../../assets/TaskItem_Botons/editar.png" alt="Edit" /> -->
          </button>
          <div v-if="showInput">
            <div class="container-edit">
              <div class="textInputWrapper">
                <input
                  v-model="newTitle"
                  placeholder="Introduce un nuevo titulo"
                  type="text"
                  class="textInput"
                />
              </div>
              <!-- <input
                type="text"
                v-model="newTitle"
                placeholder="Introduce un nuevo titulo"
              /> -->
            <div class="textInputWrapper">
                <input
                  v-model="newDescription"
                  placeholder="Introduce un nuevo descripción"
                  type="text"
                  class="textInput"
                />
              </div>
            <!-- <div>
              <p>Escribe una descripción</p>
              <input
                type="text"
                v-model="newDescription"
                placeholder="Introduce una nueva descripción"
              />
            </div> -->
            <button @click="sendData" class="button8">
              <span class="button8-content">Actualizar tarea</span>
            </button>
            <!-- <button @click="sendData"><p>Enviar datos</p></button> -->
          </div>
        </div>
        </div>
      </div>
    </div>
  </div>
  <div class="aviso-modal" v-if="showModal">
    <div class="modal">
      <img
        class="warning"
        src="../../assets/warning-icon-png-2756.png"
        alt="alert"
      />

      <div class="columns-modal">
        <h2>
          Seguro que quieres <br />
          borrar esta tarea?
        </h2>
        <div class="buttons-modal">
          <button @click="deleteTask" class="button8">
            <span class="button8-content">Si, quiero borrar! </span>
          </button>
        </div>

        <button @click="showModalToggle" class="button8">
          <span class="button8-content">No, quiero cancelar </span>
        </button>
        <!-- <button @click="deleteTask">Si quiero!</button>
    <button @click="showModalToggle">No, mejor no!</button> -->
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
};

// Función para borrar la tarea a través de la store. El problema que tendremos aquí (y en NewTask.vue) es que cuando modifiquemos la base de datos los cambios no se verán reflejados en el v-for de Home.vue porque no estamos modificando la variable tasks guardada en Home. Usad el emit para cambiar esto y evitar ningún page refresh.
const deleteTask = async() => {
    await taskStore.deleteTask(props.task.id);
    emit("updateTask");

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
  } else {
    taskStore.editTask(newTitle.value, newDescription.value, props.task.id);
    showInput.value = !showInput.value;
    emit("editChild", newTaskEdited);
  }
};

const toggleComplete = () => {
  props.task.is_complete = !props.task.is_complete;
  taskStore.completeTask(props.task.id, props.task.is_complete);
};
</script>

<style>

.taskComplete {
  font-family: Copperplate, Copperplate Gothic Light, fantasy;
}

.modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 9999;
  background: linear-gradient(145deg, #ffffff, #e6b3a5);
  width: 50vw;
  height: 50vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 5vw;
  color: white;
  animation: fade-in 0.5s forwards, modal-grow 0.5s forwards;
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

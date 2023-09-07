<script setup>
import {ref, reactive, watch, onMounted} from 'vue'
import {uid} from 'uid'
import Header from "./components/Header.vue"
import Formulario from "./components/Formulario.vue"
import Paciente from "./components/Paciente.vue"

//es un arreglo
const pacientes = ref([])
// es el objeto de paciente
const paciente =reactive({
    id: null,
    nombre: '',
    propietario: '',
    email: '',
    alta: '',
    sintomas:''
})

watch(
  pacientes, () =>{
    guardarLocalStorage();
  }, {
    deep: true // si es una estructura muy grande no sirve bien.
  }
)

onMounted(() => {
  const pacientesStorage = localStorage.getItem('pacientes')
  if(pacientesStorage) {
    paciente.value = JSON.parse(pacientesStorage)
  }
})

const guardarLocalStorage = () => {
  localStorage.setItem('paciente', JSON.stringify(pacientes.value))
}
const guardarPaciente = () => {
  if(paciente.id) {  //filtrar el id y reescribirlo
    const { id } = paciente // extrae el id de paciente el index nos permite encontrar el indice de un arreglo en base a una condicion y nos va a retornar esa posicion[].
    const i = pacientes.value.findIndex(paciente => paciente.id === id)
    pacientes.value[i] = {...paciente} //reescribe lo que teniamos en ese paciente.
  } else {
  pacientes.value.push({
  ...paciente, //lo que hace es eliminar los datos del registro aut. despues de registrarlo
  id: uid()
})  
}
 
  //reiniciar el objeto
 Object.assign(paciente, {
    nombre: '',
    propietario: '',
    email: '',
    alta: '',
    sintomas:'',
    id: null
 })
}

const actualizarPaciente = (id) => {
   const pacienteEditar = pacientes.value.filter( paciente => paciente.id === id)[0]
   Object.assign(paciente, pacienteEditar)
}

const eliminarPaciente = (id) => {
  pacientes.value = pacientes.value.filter(paciente => paciente.id !== id)
}
</script>

<template>
 <div class="container mx-auto mt-20">
  <Header/>

  <div class="mt-12 md:flex">
    <Formulario
    v-model:nombre="paciente.nombre"
    v-model:propietario="paciente.propietario" 
    v-model:email="paciente.email"
    v-model:alta="paciente.alta" 
    v-model:sintomas="paciente.sintomas"
    @guardar-paciente="guardarPaciente"
    :id="paciente.id"
    />
    <div class="md:w-1/2 md:h-screen overflow-y-scroll" >
      <h3 class="font-black text-3xl text-center"> Administra tus pacientes</h3>

      <div v-if="pacientes.length > 0">
        <p class="text-lg mt-5 text-center mb-10">
            Informacion de
            <span class="text-indigo-600 font-bold">Pacientes</span>
        </p>
      <Paciente
      v-for="paciente in pacientes"
      :paciente="paciente"
      @actualizar-paciente="actualizarPaciente"
      @eliminar-paciente="eliminarPaciente"
      />
      </div>
      <p v-else class="mt-20 text-2xl text-center"> No hay pacientes</p>
    </div>
  </div>
 </div>
</template>


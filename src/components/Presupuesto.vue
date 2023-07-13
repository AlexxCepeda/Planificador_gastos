<script setup>
import { ref } from 'vue';
import Alerta from './Alerta.vue'

const presupuesto = ref(0);
const error = ref('')

const emit = defineEmits(['definir-presupuesto'])

const definirPresupuesto = () => {
    if(presupuesto.value <= 0 || presupuesto.value === ''){
        error.value = 'Presupuesto no válido'
        setTimeout(()=>{
            error.value = ''
        }, 3000)
        return 
    }
    emit('definir-presupuesto', presupuesto.value)
}

const handleInput = e =>{
    presupuesto.value = Number(e.target.value);
}
</script>

<template>
  <form 
  class="presupuesto"
  @submit.prevent="definirPresupuesto">
  <Alerta v-if="error">
    {{ error }}
  </Alerta>
    <div class="campo">
        <label for="definir-presupuesto">
            Definir presupuesto
        </label>
        <input type="number" 
        id="definir-presupuesto"
        placeholder="Añade tu presupuesto"
        min="0"
        @input="handleInput">
    </div>
    <input type="submit" value="Definir presupuesto">
  </form>
</template>

<style scoped>
    .presupuesto{
        width: 100%;
    }
    .campo{
        display: grid;
        gap: 1.5rem;
    }
    .presupuesto label{
        color: var(--gris-oscuro);
        font-weight: 500;
    }
    .presupuesto input[type="number"]{
        background-color: var(--gris-claro);
        border-radius: 1rem;
        padding: 1rem;
        text-align: center;
        font-size: 1.8rem;
        line-height: 2.1rem;
        border: none;
    }
    .presupuesto input[type="submit"]{
        border: none;
        text-align: center;
        background-color: var(--azul);
        font-weight: 700;
        padding: 1rem;
        width: 100%;
        border-radius: 0.5em;
        color: var(--blanco);
        margin-top: 2rem;
        font-size: 1.8rem;
        line-height: 2rem;
        transition: background-color 300ms ease;
    }
    .presupuesto input[type="submit"]:hover{
        background-color: #3263b3;
        cursor: pointer;
    }
</style>
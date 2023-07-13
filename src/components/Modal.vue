<script setup>
import { ref, computed } from "vue";
import Alerta from "./Alerta.vue";
import cerrarModal from "../assets/cerrar.svg";

const props = defineProps({
  modal: {
    type: Object,
    required: true,
  },
  nombre: {
    type: String,
    required: true,
  },
  cantidad: {
    type: [String, Number],
    required: true,
  },
  categoria: {
    type: String,
    required: true,
  },
  gasto: {
    type: Object,
    required: true,
  },
});

const error = ref("");

const emit = defineEmits([
  "ocultar-modal",
  "guardar-gasto",
  "borrar-gasto",
  "update:nombre",
  "update:cantidad",
  "update:categoria",
]);

const agregarGasto = () => {
  const { nombre, cantidad, categoria } = props;
  if ([nombre, cantidad, categoria].includes("")) {
    error.value = "Todos los campos son obligatorios";
    setTimeout(() => {
      error.value = "";
    }, 3000);
    return;
  }
  if (cantidad <= 0) {
    error.value = "Cantidad no válida";
    setTimeout(() => {
      error.value = "";
    }, 3000);
    return;
  }
  emit("guardar-gasto");
};

const isEditing = computed(() => {
  return props.gasto.id;
});
</script>

<template>
  <div class="modal">
    <div class="cerrar-modal">
      <img
        :src="cerrarModal"
        alt="cerrar modal"
        @click="$emit('ocultar-modal')"
      />
    </div>
    <div
      class="contenedor contenedor-formulario"
      :class="[modal.animar ? 'animar' : 'cerrar']"
    >
      <form class="nuevo-gasto" @submit.prevent="agregarGasto">
        <legend>{{ isEditing ? "Actualizar gasto" : "Añadir gasto" }}</legend>
        <Alerta v-if="error" class="errorAlerta">
          {{ error }}
        </Alerta>
        <div class="campo">
          <label for="nombre">Nombre gasto:</label>
          <input
            type="text"
            id="nombre"
            placeholder="Añade el nombre del gasto"
            :value="nombre"
            @input="$emit('update:nombre', $event.target.value)"
          />
        </div>
        <div class="campo">
          <label for="cantidad">Cantidad:</label>
          <input
            type="number"
            id="cantidad"
            placeholder="Añade la cantidad del gasto, ej. 300"
            min="0"
            :value="cantidad"
            @input="$emit('update:cantidad', +$event.target.value)"
          />
        </div>
        <div class="campo">
          <label for="categoria">Categoría:</label>
          <select
            id="categoria"
            :value="categoria"
            @input="$emit('update:categoria', $event.target.value)"
          >
            <option value="">Selecciona una opción</option>
            <option value="ahorro">Ahorro</option>
            <option value="comida">Comida</option>
            <option value="casa">Casa</option>
            <option value="salud">Salud</option>
            <option value="ocio">Ocio</option>
            <option value="suscripciones">Suscripciones</option>
            <option value="gastos">Otros gastos</option>
          </select>
        </div>
        <input
          type="submit"
          :value="[isEditing ? 'Actualizar gasto' : 'Añadir gasto']"
        />
      </form>
      <button
        v-if="isEditing"
        type="button"
        class="btn-eliminar"
        @click="$emit('borrar-gasto')"
      >
        Eliminar gasto
      </button>
    </div>
  </div>
</template>

<style scoped>
.modal {
  position: absolute;
  background-color: rgb(0 0 0 / 0.8);
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}
.cerrar-modal {
  position: absolute;
  right: 3rem;
  top: 3rem;
}
.cerrar-modal img {
  width: 3rem;
  cursor: pointer;
}
.contenedor-formulario {
  transition: all 200ms ease-in;
}
.contenedor-formulario.animar {
  opacity: 1;
}
.contenedor-formulario.cerrar {
  opacity: 0;
}
.nuevo-gasto {
  margin: 10rem auto 0 auto;
  display: grid;
  gap: 2rem;
}
.nuevo-gasto legend {
  text-align: center;
  color: var(--blanco);
  font-weight: 700;
  font-size: 3rem;
}
.campo {
  display: grid;
  gap: 2rem;
}
.nuevo-gasto input,
.nuevo-gasto select {
  background-color: var(--gris-claro);
  border: none;
  padding: 1rem;
  font-size: 1.8rem;
  line-height: 2rem;
  border-radius: 1rem;
}

.nuevo-gasto label {
  color: var(--blanco);
  font-size: 2.5rem;
  line-height: 3rem;
}
.nuevo-gasto input[type="submit"] {
  background-color: var(--azul);
  color: var(--blanco);
  font-weight: 600;
  cursor: pointer;
  border: 1px solid var(--azul);
}
.nuevo-gasto input[type="submit"]:hover,
input[type="submit"]:active {
  background-color: initial;
  color: var(--azul);
}
input[type="submit"]:active {
  opacity: 0.5;
}
.errorAlerta {
  background-color: rgb(218, 18, 18);
  color: var(--blanco);
  border: none;
}

.btn-eliminar {
  width: 75%;
  margin: 0 auto;
  margin-top: 6rem;
  background: #ff4742;
  border: 1px solid #ff4742;
  border-radius: 6px;
  color: #ffffff;
  cursor: pointer;
  display: block;
  font-size: 12px;
  font-weight: 600;
  line-height: 16px;
  min-height: 40px;
  padding: 1rem;
}

.btn-eliminar:hover,
.btn-eliminar:active {
  background-color: initial;
  color: #ff4742;
}

.btn-eliminar:active {
  opacity: 0.5;
}
</style>

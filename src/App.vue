<script setup>
import { ref, reactive, watch, computed, onMounted } from "vue";
import Presupuesto from "./components/Presupuesto.vue";
import ControlPresupuesto from "./components/ControlPresupuesto.vue";
import Modal from "./components/Modal.vue";
import Gasto from "./components/Gasto.vue";
import Filtros from "./components/Filtros.vue";

import iconoNuevoGasto from "./assets/nuevo-gasto.svg";
import { generarId } from "./helpers";

const modal = reactive({
  mostrar: false,
  animar: false,
});
const presupuesto = ref(0);
const gastado = ref(0);
const disponible = ref(0);
const filtro = ref("");

const gasto = reactive({
  nombre: "",
  cantidad: "",
  categoria: "",
  id: null,
  fecha: Date.now(),
});

const gastos = ref([]);

watch(
  gastos,
  () => {
    const totalGastado = gastos.value.reduce(
      (total, gasto) => gasto.cantidad + total,
      0
    );
    gastado.value = totalGastado;
    disponible.value = presupuesto.value - totalGastado;
    localStorage.setItem("gastos", JSON.stringify(gastos.value));
  },
  {
    deep: true,
  }
);

watch(presupuesto, () => {
  localStorage.setItem("presupuesto", presupuesto.value);
});

onMounted(() => {
  const presupuestoStorage = localStorage.getItem("presupuesto");
  if (presupuestoStorage) {
    presupuesto.value = Number(presupuestoStorage);
    disponible.value = Number(presupuestoStorage);
  }
  const gastosStorage = localStorage.getItem("gastos");
  if (gastosStorage) {
    gastos.value = JSON.parse(gastosStorage);
  }
  // if (gastos.value.length === 0) {
  //   disponible.value = Number(presupuestoStorage);
  // }
});

const definirPresupuesto = (cantidad) => {
  presupuesto.value = cantidad;
  disponible.value = cantidad;
};

const mostrarModal = () => {
  modal.mostrar = true;
  setTimeout(() => {
    modal.animar = true;
  }, 300);
};
const ocultarModal = () => {
  modal.animar = false;
  setTimeout(() => {
    modal.mostrar = false;
  }, 300);
  gasto.nombre = "";
  gasto.cantidad = "";
  gasto.categoria = "";
  gasto.fecha = Date.now();
  setTimeout(() => {
    gasto.id = null;
  }, 300);
};

const guardarGasto = () => {
  if (gasto.id) {
    const { id } = gasto;
    const i = gastos.value.findIndex((gasto) => gasto.id === id);
    gastos.value[i] = { ...gasto };
  } else {
    gastos.value.push({
      ...gasto,
      id: generarId(),
    });
  }
  ocultarModal();
};

const seleccionarGasto = (id) => {
  const gastoEditar = gastos.value.filter((gasto) => gasto.id === id)[0];
  Object.assign(gasto, gastoEditar);
  mostrarModal();
};

const eliminarGasto = () => {
  if (confirm("¿Eliminar?")) {
    gastos.value = gastos.value.filter(
      (gastoTemp) => gastoTemp.id !== gasto.id
    );
    ocultarModal();
  }
};

const gastosFiltrados = computed(() => {
  if (filtro.value) {
    return gastos.value.filter((gasto) => gasto.categoria === filtro.value);
  }
  return gastos.value;
});

const resetApp = () => {
  if (confirm("Estás a punto de reiniciar la aplicación")) {
    gastos.value = [];
    presupuesto.value = 0;
  }
};
</script>

<template>
  <div :class="{ fijar: modal.mostrar }">
    <header>
      <h1 class="scale-up-center">Planificador de gastos</h1>
      <div class="contenedor-header contenedor sombra">
        <Presupuesto
          v-if="presupuesto === 0"
          @definir-presupuesto="definirPresupuesto"
        />
        <ControlPresupuesto
          v-else
          @resetear-app="resetApp"
          :presupuesto="presupuesto"
          :disponible="disponible"
          :gastado="gastado"
          class="scale-up-center"
        />
      </div>
    </header>
    <main v-if="presupuesto">
      <div class="listado-gastos contenedor">
        <div class="main_filter">
          <h2>{{ gastos.length > 0 ? "Gastos" : "No hay gastos" }}</h2>
          <Filtros v-if="gastos.length > 0" v-model:filtro="filtro" />
        </div>
        <h2
          v-if="gastosFiltrados.length === 0 && filtro !== ''"
          class="ningunGasto"
        >
          No hay gastos en {{ filtro }}
        </h2>
        <Gasto
          v-for="gasto in gastosFiltrados"
          :key="gasto.id"
          :gasto="gasto"
          @seleccionar-gasto="seleccionarGasto"
        />
      </div>
      <div class="crear-gasto">
        <img
          :src="iconoNuevoGasto"
          alt="icono nuevo gasto"
          @click="mostrarModal"
        />
      </div>
      <Modal
        v-if="modal.mostrar"
        @ocultar-modal="ocultarModal"
        @guardar-gasto="guardarGasto"
        @borrar-gasto="eliminarGasto"
        :modal="modal"
        v-model:nombre="gasto.nombre"
        v-model:cantidad="gasto.cantidad"
        v-model:categoria="gasto.categoria"
        :gasto="gasto"
      />
    </main>
  </div>
</template>

<style>
:root {
  --azul: #3b82f6;
  --blanco: #fff;
  --gris-claro: #f5f5f5;
  --gris: #94a3b8;
  --gris-oscuro: #64748b;
  --negro: #000;
}
html {
  font-size: 62.5%;
  box-sizing: border-box;
}
*,
*:before,
*:after {
  box-sizing: inherit;
}
body {
  font-size: 1.6rem;
  font-family: "Satoshi", sans-serif;
  background-color: var(--gris-claro);
}
h1 {
  font-size: 3.2rem;
}
h2 {
  font-size: 3rem;
}
.fijar {
  overflow: hidden;
  height: 100vh;
}
header {
  background-color: var(--azul);
}
header h1 {
  padding: 3rem 0;
  margin: 0;
  color: var(--blanco);
  text-align: center;
}
.contenedor {
  width: 90%;
  max-width: 80rem;
  margin: 0 auto;
}
.contenedor-header {
  margin-top: -5rem;
  transform: translateY(5rem);
  padding: 3rem;
}
.sombra {
  box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
  background-color: var(--blanco);
  border-radius: 1.2rem;
  padding: 3rem;
}
.crear-gasto {
  position: fixed;
  bottom: 5rem;
  right: 5rem;
}
.crear-gasto img {
  width: 5rem;
  cursor: pointer;
}
.listado-gastos {
  margin-top: 10rem;
}
.listado-gastos h2 {
  font-size: 700;
  color: var(--gris-oscuro);
}

.main_filter {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.ningunGasto {
  text-align: center;
  margin-bottom: 10rem;
}
.scale-up-center {
  -webkit-animation: scale-up-center 0.4s cubic-bezier(0.39, 0.575, 0.565, 1)
    both;
  animation: scale-up-center 0.4s cubic-bezier(0.39, 0.575, 0.565, 1) both;
}

@-webkit-keyframes scale-up-center {
  0% {
    -webkit-transform: scale(0.5);
    transform: scale(0.5);
  }
  100% {
    -webkit-transform: scale(1);
    transform: scale(1);
  }
}
@keyframes scale-up-center {
  0% {
    -webkit-transform: scale(0.5);
    transform: scale(0.5);
  }
  100% {
    -webkit-transform: scale(1);
    transform: scale(1);
  }
}

@media screen and (min-width: 550px) {
  h1 {
    font-size: 4.6rem;
  }
  .contenedor-header {
    padding: 5rem;
  }
  .sombra {
    padding: 5rem;
  }
}
</style>

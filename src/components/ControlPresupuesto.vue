<script setup>
import { computed } from "vue";
import CircleProgress from "vue3-circle-progress-bar";
import { formatearCantidad } from "../helpers";
const props = defineProps({
  presupuesto: {
    type: Number,
    required: true,
  },
  gastado: {
    type: Number,
    required: true,
  },
  disponible: {
    type: Number,
    required: true,
  },
});
defineEmits(["resetear-app"]);

const porcentajeDisponible = computed(() => {
  return parseInt(100 - (props.gastado * 100) / props.presupuesto);
});

const colorRelleno = computed(() => {
  return props.disponible > 0 ? "#3b82f6" : "#ff4742";
});
</script>

<template>
  <div class="dos-columnas">
    <div class="contenedor-grafico">
      <p class="porcentaje">{{ porcentajeDisponible }}%</p>
      <CircleProgress
        :percent="porcentajeDisponible"
        :border-width="30"
        :border-bg-width="30"
        :fill-color="colorRelleno"
        empty-color="#f5f5f5"
      />
    </div>
    <div class="contenedor-presupuesto">
      <button type="button" class="reset-app" @click="$emit('resetear-app')">
        Resetear app
      </button>
      <p>
        <span>Presupuesto: </span>
        {{ formatearCantidad(presupuesto) }}
      </p>
      <p>
        <span>Disponible:</span>
        {{ formatearCantidad(disponible) }}
      </p>
      <p>
        <span>Gastado:</span>
        {{ formatearCantidad(gastado) }}
      </p>
    </div>
  </div>
</template>

<style scoped>
.contenedor-grafico {
  position: relative;
}
.porcentaje {
  position: absolute;
  margin: auto;
  top: calc(50% - 1.5rem);
  left: 0;
  right: 0;
  text-align: center;
  z-index: 100;
  font-size: 2.5rem;
  font-weight: 600;
  color: var(--gris-oscuro);
}
.dos-columnas {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.dos-columnas > :first-child {
  margin-bottom: 3rem;
}
.reset-app {
  width: 100%;
  padding: 0.7rem;
  font-size: 1.5rem;
  border: none;
  border-radius: 0.75rem;
  background-color: #db2777;
  font-weight: 700;
  color: var(--blanco);
  transition: background-color 300ms ease;
  text-transform: uppercase;
}
.reset-app:hover {
  cursor: pointer;
  background-color: #8d2d58;
}

.contenedor-presupuesto {
  width: 100%;
}
.contenedor-presupuesto p {
  font-size: 1.8rem;
  line-height: 2.2rem;
  text-align: left;
  letter-spacing: 0.8px;
}
.contenedor-presupuesto span {
  font-weight: 800;
  color: var(--azul);
}

@media screen and (min-width: 550px) {
  .contenedor-presupuesto p {
    font-size: 2.4rem;
    line-height: 2.8rem;
  }
  .reset-app {
    padding: 1rem;
    font-size: 1.8rem;
  }
}
@media screen and (min-width: 768px) {
  .dos-columnas {
    flex-direction: row;
    gap: 4rem;
  }
  .dos-columnas > :first-child {
    margin-bottom: 0;
  }
  .contenedor-presupuesto p {
    text-align: left;
  }
}
</style>

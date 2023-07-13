<script setup>
import {
  formatearCantidad,
  formatearFecha,
  primeraMayuscula,
} from "../helpers";
import IconoAhorro from "../assets/icono_ahorro.svg";
import IconoCasa from "../assets/icono_casa.svg";
import IconoComida from "../assets/icono_comida.svg";
import IconoGastos from "../assets/icono_gastos.svg";
import IconoOcio from "../assets/icono_ocio.svg";
import IconoSalud from "../assets/icono_salud.svg";
import IconoSuscripciones from "../assets/icono_suscripciones.svg";

const diccionarioIconos = {
  ahorro: IconoAhorro,
  comida: IconoComida,
  casa: IconoCasa,
  gastos: IconoGastos,
  ocio: IconoOcio,
  salud: IconoSalud,
  suscripciones: IconoSuscripciones,
};

const props = defineProps({
  gasto: {
    type: Object,
    required: true,
  },
});

defineEmits(["seleccionar-gasto"]);
</script>

<template>
  <div class="gasto-card sombra animated slideInLeft">
    <div class="card_flex">
      <div class="card_imagen">
        <img
          :src="diccionarioIconos[gasto.categoria]"
          :alt="'Icono' + gasto.categoria"
        />
      </div>
      <div class="card_contenido" @click="$emit('seleccionar-gasto', gasto.id)">
        <h3>{{ primeraMayuscula(gasto.nombre) }}</h3>
        <p class="contenido_categoria">
          {{ primeraMayuscula(gasto.categoria) }}
        </p>
        <p>{{ formatearFecha(gasto.fecha) }}</p>
      </div>
    </div>
    <h3>{{ formatearCantidad(gasto.cantidad) }}</h3>
  </div>
</template>

<style scoped>
.gasto-card {
  padding: 2rem;
  margin: 3rem 0;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.card_flex {
  display: flex;
  margin: 0;
  flex-direction: row;
  align-items: center;
  gap: 1rem;
}
.card_imagen img {
  width: 6rem;
}
.card_contenido {
  margin-left: 1rem;
  cursor: pointer;
}
.card_contenido h3,
p {
  margin: 0;
  padding: 0;
}
.contenido_categoria {
  margin: 0.5rem 0;
}

@-webkit-keyframes slideInLeft {
  from {
    -webkit-transform: translate3d(-100%, 0, 0);
    transform: translate3d(-100%, 0, 0);
    visibility: visible;
  }

  to {
    -webkit-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);
  }
}

@keyframes slideInLeft {
  from {
    -webkit-transform: translate3d(-100%, 0, 0);
    transform: translate3d(-100%, 0, 0);
    visibility: visible;
  }

  to {
    -webkit-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);
  }
}

.slideInLeft {
  -webkit-animation-name: slideInLeft;
  animation-name: slideInLeft;
}

.animated {
  -webkit-animation-duration: 1s;
  animation-duration: 1s;
  -webkit-animation-fill-mode: both;
  animation-fill-mode: both;
}
@media screen and (min-width: 550px) {
  .card_imagen img {
    width: 8rem;
  }
  .contenido_categoria {
    margin: 0.7rem 0;
  }
}
</style>

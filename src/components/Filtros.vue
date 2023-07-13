<script setup>
defineProps(["filtro"]);
defineEmits(["update:filtro"]);
</script>

<template>
  <div class="filtros">
    <form>
      <select
        id="filtro"
        class="selector"
        :value="filtro"
        @input="$emit('update:filtro', $event.target.value)"
      >
        <option value="">Mostrar todos</option>
        <option value="ahorro">Ahorro</option>
        <option value="comida">Comida</option>
        <option value="casa">Casa</option>
        <option value="salud">Salud</option>
        <option value="ocio">Ocio</option>
        <option value="suscripciones">Suscripciones</option>
        <option value="gastos">Otros gastos</option>
      </select>
    </form>
  </div>
</template>

<style scoped>
.selector {
  padding: 6px 30px;
  background-color: var(--gris-claro);
}
</style>

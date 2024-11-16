<template>
  <div id="app" class="container">
    <h1 class="title">Selecciona un Reporte</h1>

    <!-- Botones para cargar los reportes -->
    <div class="button-container">
      <button @click="loadReport(1)">Reporte 1</button>
      <button @click="loadReport(2)">Reporte 2</button>
      <button @click="loadReport(3)">Reporte 3</button>
      <button @click="loadReport(4)">Reporte 4</button>
      <button @click="loadReport(5)">Reporte 5</button>
      <button @click="loadReport(6)">Reporte 6</button>
    </div>

    <!-- Mostrar el reporte cargado (solo cuando se active el reporte) -->
    <div v-if="activeReport === 1">
      <Reporte1 v-if="reportLoaded" />
    </div>
    <div v-if="activeReport === 2">
      <Reporte2 v-if="reportLoaded" />
    </div>
    <div v-if="activeReport === 3">
      <Reporte3 v-if="reportLoaded" />
    </div>
    <div v-if="activeReport === 4">
      <Reporte4 v-if="reportLoaded" />
    </div>
    <div v-if="activeReport === 5">
      <Reporte5 v-if="reportLoaded" />
    </div>
    <div v-if="activeReport === 6">
      <Reporte6 v-if="reportLoaded" />
    </div>
  </div>
</template>

<script>
import Reporte1 from '@/views/uikit/Reporte1.vue';
import Reporte2 from '@/views/uikit/Reporte2.vue';
import { ref } from 'vue';

export default {
  components: {
    Reporte1,
    Reporte2
  },
  setup() {
    const activeReport = ref(0); // No hay reporte cargado inicialmente
    const reportLoaded = ref(false); // Determina si el reporte está cargado

    const loadReport = (reportNumber) => {
      activeReport.value = reportNumber;
      reportLoaded.value = false; // Resetear la carga cuando cambiamos el reporte

      // Después de un pequeño retraso (simulando carga de datos), cargar el reporte
      setTimeout(() => {
        reportLoaded.value = true;
      }, 300); // Simulando la carga con un pequeño retraso
    };

    return {
      activeReport,
      loadReport,
      reportLoaded,
    };
  },
};
</script>

<style scoped>
/* Estilos para la vista principal con los botones y el contenedor */
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f7f7f7;
  border-radius: 10px;
}

.title {
  text-align: center;
  font-size: 2.5em;
  color: #333;
  margin-bottom: 20px;
  text-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
}

.button-container {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 20px;
}

button {
  padding: 10px 20px;
  font-size: 1.2em;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}
</style>

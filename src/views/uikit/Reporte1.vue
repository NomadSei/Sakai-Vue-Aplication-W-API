<template>
    <div id="app" class="container">
      <h1 class="title">Tiempo Total Lotes</h1>
  
        <div class="message" v-if="loading">
        <div class="spinner"></div>
        Cargando datos...
        </div>
        <div class="message error" v-else-if="error">{{ error }}</div>
  
       <!-- DataTable con los datos de fermentaciones -->
        <transition name="fade">
            <DataTable v-if="!loading && !error" :value="fermentaciones" :paginator="true" :rows="10" :responsive="true" :filterDisplay="'row'">
                <Column field="id" header="ID"></Column>
                <Column field="tanque" header="Tanque"></Column>
                <Column field="temp_minima" header="Temp Mínima"></Column>
                <Column field="temp_maxima" header="Temp Máxima"></Column>
                <Column field="tiempo_fermentacion" header="Tiempo de Fermentación"></Column>
            </DataTable>
        </transition>
  
      <!-- Gráfico de barras -->
      <div v-if="!loading && !error" class="chart-container">
        <canvas id="myChart"></canvas>
      </div>
    </div>
  </template>
  
<script>
    import Chart from 'chart.js/auto'; // Asegúrate de importar Chart.js
import { nextTick, onMounted, ref } from 'vue';
  
  export default {
    setup() {
      const fermentaciones = ref([]);
      const loading = ref(true);
      const error = ref(null);
  
      onMounted(() => {
        fetch('http://localhost:8080')
          .then(response => {
            if (!response.ok) {
              throw new Error('Error al cargar los datos: ' + response.statusText);
            }
            return response.json();
          })
          .then(data => {
            if (data && Array.isArray(data.fermentaciones)) {
              fermentaciones.value = data.fermentaciones;
              loading.value = false;
              console.log("Datos cargados:", data.fermentaciones); // Verifica los datos cargados
              nextTick(() => { 
                createChart(); // Crear gráfico después de que Vue haya actualizado el DOM
              });
            } else {
              throw new Error('La estructura de datos es incorrecta');
            }
          })
          .catch(err => {
            error.value = 'Error al cargar los datos: ' + err.message;
            loading.value = false;
          });
      });
  
      const createChart = () => {
        const canvas = document.getElementById('myChart');
        if (!canvas) {
          console.error('El canvas no está disponible');
          return;
        }
  
        const labels = fermentaciones.value.map(f => f.tanque);
        const tempMinima = fermentaciones.value.map(f => parseFloat(f.temp_minima));
        const tempMaxima = fermentaciones.value.map(f => parseFloat(f.temp_maxima));
  
        new Chart(canvas, {
        type: 'bar',
        data: {
            labels: labels,
            datasets: [
            {
                label: 'Temperatura Mínima',
                data: tempMinima,
                backgroundColor: 'rgba(255, 99, 132, 0.2)',
                borderColor: 'rgba(255, 99, 132, 1)',
                borderWidth: 1,
            },
            {
                label: 'Temperatura Máxima',
                data: tempMaxima,
                backgroundColor: 'rgba(54, 162, 235, 0.2)',
                borderColor: 'rgba(54, 162, 235, 1)',
                borderWidth: 1,
            },
            ],
        },
        options: {
            responsive: true,
            animation: {
            duration: 1500, // Duración de la animación
            easing: 'easeOutBounce', // Estilo de animación
            },
            scales: {
            y: {
                beginAtZero: true,
            },
            },
        },
        });
      };
  
      return {
        fermentaciones,
        loading,
        error,
      };
    },
  };
</script>
  
  <style scoped>
  /* Tus estilos aquí */
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
  
  .message {
    text-align: center;
    font-size: 1.2em;
    color: #555;
    padding: 10px;
    margin-bottom: 20px;
    background-color: #f4f4f4;
    border-radius: 5px;
  }
  
  .error {
    color: #e74c3c;
    background-color: #fce4e4;
  }
  
  .table-container {
    overflow-x: auto;
    margin-top: 20px;
    border-radius: 10px;
    padding: 10px;
    background-color: white;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  }
  
  .chart-container {
    text-align: center;
    margin-top: 20px;
    background-color: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  }
  
  canvas {
    max-width: 100%;
    border-radius: 10px;
  }

  /* Estilos del spinner */
.spinner {
  border: 4px solid rgba(255, 255, 255, 0.3);
  border-top: 4px solid #3498db;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  animation: spin 1s linear infinite;
  margin: 0 auto 10px auto;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

/* Transición de desvanecimiento */
.fade-enter-active, .fade-leave-active {
  transition: opacity 1s ease;
}
.fade-enter, .fade-leave-to /* .fade-leave-active en <2.1.8 */
{
  opacity: 0;
}

/* Animación del mensaje de error */
.error {
  color: #e74c3c;
  background-color: #fce4e4;
  animation: bounceIn 1s ease;
}

@keyframes bounceIn {
  0% {
    opacity: 0;
    transform: translateY(-200px);
  }
  60% {
    opacity: 1;
    transform: translateY(30px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

</style>
  
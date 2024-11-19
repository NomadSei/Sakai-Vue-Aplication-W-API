<template>
    <Fluid>
        <div class="flex flex-col md:flex-row gap-8">
            <div class="md:w-1/2">
                <div class="card flex flex-col gap-4 p-6 shadow-lg rounded-lg">
                    <div class="font-semibold text-xl">Consumir API REST</div>

                    <div class="flex flex-col gap-2">
                        <label for="id" class="text-gray-700">ID</label>
                        <InputText v-model="id" id="id" type="text" class="p-2 border border-gray-300 rounded" />
                    </div>

                    <div class="flex flex-col gap-2">
                        <label for="nombre" class="text-gray-700">Nombre</label>
                        <InputText v-model="nombre" id="nombre" type="text" class="p-2 border border-gray-300 rounded" readonly />
                    </div>

                    <div class="flex flex-col gap-2">
                        <label for="color" class="text-gray-700">Color</label>
                        <InputText v-model="color" id="color" type="text" class="p-2 border border-gray-300 rounded" readonly />
                    </div>

                    <div class="flex flex-col gap-2">
                        <label for="capacidad" class="text-gray-700">Capacidad</label>
                        <InputText v-model="capacidad" id="capacidad" type="text" class="p-2 border border-gray-300 rounded" readonly />
                    </div>

                    <button
                        type="button"
                        @click="cargarProducto"
                        class="mt-4 bg-green-500 text-white py-2 px-4 rounded cursor-pointer hover:bg-green-600 transition">
                        Consultar Producto por ID
                    </button>
                    <div v-if="loading" class="text-center mt-4 text-gray-600">Cargando...</div>
                </div>
            </div>
        </div>

        <!-- Tabla para mostrar todos los productos obtenidos -->
        <div class="mt-8">
            <h2 class="font-semibold text-xl">Lista de Productos</h2>
            <DataTable :value="productos" v-model:selection="productosSeleccionados" class="p-datatable-gridlines" dataKey="id" :rows="5"
                paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                :rowsPerPageOptions="[5,10,25]" currentPageReportTemplate="Visualizando {last} de {totalRecords} productos"
                style="margin-bottom: 20px" :paginator="true" responsiveLayout="scroll">

                <Column field="id" header="ID" :sortable="true" style="width:50px"></Column>
                <Column field="name" header="Nombre" style="width:250px"></Column>
                <Column field="color" header="Color" style="width:100px" :body="colorBodyTemplate"></Column>
                <Column field="capacidad" header="Capacidad" style="width:120px" :body="capacityBodyTemplate"></Column>

            </DataTable>
        </div>
    </Fluid>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            id: '',
            nombre: '',
            color: '',
            capacidad: '',
            productos: [],
            productosSeleccionados: [], // Array para manejar la selección de productos
            loading: false
        };
    },
    computed: {
        productosOrdenados() {
            return this.productos.sort((a, b) => a.id - b.id);
        }
    },
    methods: {
        cargarProducto() {
            if (!this.id.trim()) {
                alert('Por favor, ingrese un ID de producto.');
                return;
            }

            this.loading = true;
            axios
                .get(`https://api.restful-api.dev/objects/${this.id}`)
                .then((response) => {
                    if (response.data) {
                        const producto = response.data;
                        this.nombre = producto.name || 'No disponible';
                        this.color = producto.data?.color || 'N/A';
                        this.capacidad = producto.data?.capacity || producto.data?.['capacity GB'] || 'N/A';
                    } else {
                        alert('No se encontró el producto con el ID especificado.');
                    }
                })
                .catch((error) => {
                    this.handleError(error);
                })
                .finally(() => {
                    this.loading = false;
                });
        },
        cargarProductos() {
            this.loading = true;
            axios
                .get('https://api.restful-api.dev/objects')
                .then((response) => {
                    if (Array.isArray(response.data)) {
                        this.productos = response.data.map(producto => ({
                            ...producto,
                            color: producto.data?.color || 'N/A',
                            capacidad: producto.data?.capacity || producto.data?.['capacity GB'] || 'N/A'
                        }));
                    } else {
                        alert('No se pudo obtener la lista de productos.');
                    }
                })
                .catch((error) => {
                    this.handleError(error);
                })
                .finally(() => {
                    this.loading = false;
                });
        },
        handleError(error) {
            console.error('Error al consultar la API:', error);
            if (error.response) {
                alert(`Error del servidor: ${error.response.status} - ${error.response.data}`);
            } else if (error.request) {
                alert('Error de red: no se recibió respuesta del servidor.');
            } else {
                alert('Error al procesar la solicitud: ' + error.message);
            }
        },

        colorBodyTemplate(rowData) {
            return rowData.data?.color || 'N/A';
        },
        capacityBodyTemplate(rowData) {
            return rowData.data?.capacity || rowData.data?.['capacity GB'] || 'N/A';
        }
    },
    mounted() {
        this.cargarProductos();
    }
};
</script>

<style scoped>
.card {
    background-color: white;
}
</style>


<template>
    <Fluid>
        <div class="flex flex-col md:flex-row gap-8">
            <div class="md:w-1/2">
                <div class="card flex flex-col gap-4 p-6 shadow-lg rounded-lg">
                    <div class="font-semibold text-xl">Formulario de Producto</div>

                    <!-- Campo de ID para ingresar el ID del producto -->
                    <div class="flex flex-col gap-2">
                        <label for="id" class="text-gray-700">ID</label>
                        <InputText v-model="id" id="id" type="text" class="p-2 border border-gray-300 rounded" />
                    </div>

                    <!-- Campo para mostrar el nombre del producto -->
                    <div class="flex flex-col gap-2">
                        <label for="nombre" class="text-gray-700">Nombre</label>
                        <InputText v-model="nombre" id="nombre" type="text" class="p-2 border border-gray-300 rounded" readonly />
                    </div>

                    <!-- Campo para mostrar el color del producto -->
                    <div class="flex flex-col gap-2">
                        <label for="color" class="text-gray-700">Color</label>
                        <InputText v-model="color" id="color" type="text" class="p-2 border border-gray-300 rounded" readonly />
                    </div>

                    <!-- Campo para mostrar la capacidad del producto -->
                    <div class="flex flex-col gap-2">
                        <label for="capacidad" class="text-gray-700">Capacidad</label>
                        <InputText v-model="capacidad" id="capacidad" type="text" class="p-2 border border-gray-300 rounded" readonly />
                    </div>

                    <!-- Botón para consultar un producto por ID -->
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
            <table class="min-w-full bg-white border border-gray-300 rounded mt-4">
                <thead>
                    <tr>
                        <th class="py-2 px-4 border-b">ID</th>
                        <th class="py-2 px-4 border-b">Nombre</th>
                        <th class="py-2 px-4 border-b">Color</th>
                        <th class="py-2 px-4 border-b">Capacidad</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-if="productos.length === 0">
                        <td colspan="4" class="py-2 px-4 border-b text-center text-gray-500">No hay productos disponibles</td>
                    </tr>
                    <tr v-else v-for="producto in productosOrdenados" :key="producto.id">
                        <td class="py-2 px-4 border-b">{{ producto.id }}</td>
                        <td class="py-2 px-4 border-b">{{ producto.name }}</td>
                        <td class="py-2 px-4 border-b">{{ producto.data?.color || 'N/A' }}</td>
                        <td class="py-2 px-4 border-b">{{ producto.data?.capacity || producto.data?.['capacity GB'] || 'N/A' }}</td>
                    </tr>
                </tbody>
            </table>
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
            loading: false // Indicador de carga
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
                .get(`https://api.restful-api.dev/objects/${this.id}`) // Usa backticks
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
                alert(`Error del servidor: ${error.response.status} - ${error.response.data}`); // Corrige la interpolación
            } else if (error.request) {
                alert('Error de red: no se recibió respuesta del servidor.');
            } else {
                alert('Error al procesar la solicitud: ' + error.message);
            }
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

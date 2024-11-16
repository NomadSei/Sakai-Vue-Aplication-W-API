<script setup>
import { ref } from 'vue';

const nombre = ref('');
const apellidoPaterno = ref('');
const apellidoMaterno = ref('');
const fechaNacimiento = ref('');
const rfc = ref('');
const calendarValue = ref(null);

const generarRFC = () => {
    const paterno = apellidoPaterno.value.toUpperCase();
    const materno = apellidoMaterno.value.toUpperCase() || 'X';
    const nombrePrimero = nombre.value.toUpperCase();

    const inicialPaterno = paterno.charAt(0);
    const vocalInterna = paterno.slice(1).replace(/[^AEIOU]/g, '').charAt(0) || 'X';
    const inicialMaterno = materno.charAt(0);
    const inicialNombre = nombrePrimero.charAt(0);

    const fecha = new Date(calendarValue.value);
    const anio = String(fecha.getFullYear()).slice(-2);
    const mes = String(fecha.getMonth() + 1).padStart(2, '0');
    const dia = String(fecha.getDate()).padStart(2, '0');

    rfc.value = `${inicialPaterno}${vocalInterna}${inicialMaterno}${inicialNombre}${anio}${mes}${dia}`;
};
</script>

<template>
    <Fluid>
        <div class="card flex flex-col gap-4 p-6">
            <div class="font-semibold text-xl text-center">GENERAR EL RFC DE UNA PERSONA</div>

            <div class="flex flex-col gap-2">
                <label for="nombre">Nombre(s):</label>
                <InputText id="nombre" type="text" v-model="nombre" />
            </div>

            <div class="flex flex-col gap-2">
                <label for="apellidoPaterno">Apellido Paterno:</label>
                <InputText id="apellidoPaterno" type="text" v-model="apellidoPaterno" />
            </div>

            <div class="flex flex-col gap-2">
                <label for="apellidoMaterno">Apellido Materno:</label>
                <InputText id="apellidoMaterno" type="text" v-model="apellidoMaterno" />
            </div>

            <div class="flex flex-col gap-2">
                <label for="fechaNacimiento">Fecha Nacimiento:</label>
                <div class="font-semibold text-xl">DatePicker</div>
                <DatePicker :showIcon="true" :showButtonBar="true" v-model="calendarValue" />
            </div>

            <div class="flex flex-col gap-2">
                <label for="rfc">RFC</label>
                <InputText id="rfc" type="text" v-model="rfc" readonly />
            </div>

            <Button label="GENERAR" class="bg-green-500 text-white mt-4" @click="generarRFC"></Button>
        </div>
    </Fluid>
</template>

<style scoped>
.card {
    max-width: 500px;
    margin: auto;
    border-radius: 8px;
    background-color: white;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
</style>

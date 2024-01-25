<script setup>
import {ref, reactive, onMounted, watch} from 'vue'
import {db} from './data/guitarras'
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'

const guitarras = ref([])
const carrito = ref([])
const guitarra = ref({})

watch(carrito, () => {

 guardarLocalStorage(); //se ejecuta cada vez que algo cambia en carrito

}, {
    deep: true, //entra al objeto para ver si contienen lo mismo
})

onMounted(() =>{
    guitarras.value = db
    guitarra.value = db[4]

    const carritoStorage = localStorage.getItem('carrito')
    if(carritoStorage) {
        carrito.value  = JSON.parse(carritoStorage)
    }
})

const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

const agregarCarrito = (guitarra) => {
    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
    console.log(existeCarrito)
    if (existeCarrito >= 0){
        carrito.value[existeCarrito].cantidad++;
    }else{
        guitarra.cantidad = 1;
        carrito.value.push(guitarra); //si esta e script hace falta el .value, si está en templates no.  
    }
    guardarLocalStorage();
}

const decrementarCantidad = (id) =>{
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index].cantidad <= 1) return;
    carrito.value[index].cantidad--
}

const incrementarCantidad = (id) =>{
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index].cantidad >= 5) return;
    carrito.value[index].cantidad++
}

const eliminarProducto = (id) =>{
    carrito.value = carrito.value.filter(producto => producto.id !== id)
}

const vaciarCarrito = () =>{
    carrito.value = [];
}

</script>

<template>
    <Header 
    :carrito="carrito"
    :guitarra="guitarra"
    @decrementar-cantidad="decrementarCantidad"
    @incrementar-cantidad="incrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
    />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
           <Guitarra
           v-for="guitarra in guitarras"
           :guitarra="guitarra"
           @agregar-carrito="agregarCarrito"
           />
        </div>
    </main>
    <Footer />
</template>

<style scope>

</style>

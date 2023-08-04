<script setup>
    import {ref, reactive, onMounted, watch} from 'vue'
    import {db} from './data/guitarras'
    import Guitarra from './components/Guitarra.vue'
    import Header from './components/Header.vue'
    import Footer from './components/Footer.vue'

    //Reactive, solo para objetos que se relacionen
    // let state = reactive({
    //     guitarras: db
    // })

    // console.log(state.guitarras)

    //Ref
    let guitarras = ref([])
    let carrito = ref([])
    let guitarra = ref({})

    watch(carrito, () => {
        carritoLocalStorage()
    }, {deep: true})

    onMounted(() => {
        guitarras.value = db
        guitarra.value = db[3]

        let carritoStorage = localStorage.getItem('carrito')
        if(carritoStorage) {
            carrito.value = JSON.parse(carritoStorage)
        }
    })

    let carritoLocalStorage = () => {
        localStorage.setItem('carrito', JSON.stringify(carrito.value))
    }

    let agregarCarrito = (guitarra) => {
        let existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
        if(existeCarrito >= 0) {
            carrito.value[existeCarrito].cantidad++
        } else {
            guitarra.cantidad = 1
            carrito.value.push(guitarra)
        }
    }

    let disminuirCantidad = (id) => {
        let index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad <= 1) return
        carrito.value[index].cantidad--
    }

    let aumentarCantidad = (id) => {
        let index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad >= 5) return
        carrito.value[index].cantidad++
    }

    let eliminarProducto = (id) => {
        carrito.value = carrito.value.filter(producto => producto.id !== id)
    }

    let vaciarCarrito = () => {
        carrito.value = []
    }
</script>

<template>
    <Header 
        :carrito="carrito"
        :guitarra="guitarra"
        @disminuir-cantidad="disminuirCantidad"
        @aumentar-cantidad="aumentarCantidad"
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

<style scoped>

</style>

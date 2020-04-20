<template>
    <div>
        <h1>Тестовое задание Vue.js</h1>
        <button class="btn" @click.prevent="getProducts">Get Products</button>
        <button class="btn" @click.prevent="getProducts5000">Get Products 5000</button>
        <Table :items="products" @deleteItems="(items) => deleteItems(items)"/>
    </div>

</template>

<script>
    import Table from '../components/Table'
    import {mapMutations, mapState} from 'vuex'
    import {getProducts, deleteProducts} from '../api/request'

    export default {
        components: {
            Table
        },
        computed: {
            ...mapState(['products'])
        },
        methods: {
            ...mapMutations(['setProducts']),

            async deleteItems(items){
                await deleteProducts()
                    .then(() => this.setProducts(this.products.filter(p => !items.some(i => i.id === p.id ))))
                    .catch(error => alert('Error delete'))

            },
            getProducts() {
                const products = getProducts()
                    .then(data => this.setProducts(data))
                    .catch(error => alert('Server error'))
            },
            getProducts5000() {
                const products = getProducts()
                    .then(data => this.setProducts([...data, ...data, ...data, ...data, ...data]))
                    .catch(error => alert('Server error'))
            }
        }

    }
</script>

<style>
    .btn {
        background-color: lightseagreen;
        color: white;
        border-radius: 50px;
        padding: 5px;
        cursor: pointer;
    }

    .btn.btn-selected {
        background: orangered;
    }
</style>
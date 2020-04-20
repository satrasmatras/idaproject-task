<template>
    <div class="container">

        <div class="row">
            Table UI
        </div>

        <hr/>

        <div class="row sm-col-12">
            <div class="col-6">
                Sorting by
                <button v-for="(column, i) in showedColumns"
                     :key="i"
                     @click.prevent="() => setSortColumn(column.value)"
                     class="btn" v-bind:class="{'btn-selected': column.value === sortingValue}"
                >
                    {{column.title}}

                </button>
            </div>

            <button @click="() => deleteItems(selectedItems)">Delete BTN{{selectedItems.length ? `(${selectedItems.length})` : ''}}</button>

            <div class="col-6">
                <button class="btn"  @click.prevent="() => page--">Back</button>
                {{this.rowsPerPage*this.page + 1}}-{{this.rowsPerPage*(this.page+1)}} from {{this.items.length}}
                <button class="btn"  @click.prevent="() => page++">Forward</button>
                <select v-model="rowsPerPage">
                    <option v-for="variant in rowsPerPageVariants">
                        {{variant}}
                    </option>
                    <option :value="this.items.length">
                        All {{items.length}}
                    </option>

                </select>
                {{selectedColumns.length}} columns selected
                <select multiple v-model="selectedColumns">
                    <option
                            v-for="column in columns"
                            v-bind:value="column"
                            :selected="true">
                        {{column.title}}
                    </option>
                </select>
            </div>

        </div>
        <div class="row">
            <table>
                <th>
                    <input type="checkbox" :checked="allItemsAreSelected" @click="selectAllItems"/>
                </th>
                <th v-for="(column) in showedColumns">
                    {{column.title}}
                </th>
                <tr v-for="(item) in sortedItems" class="table-row" :key="item.id" @click="() => selectRow(item)">

                    <td>
                        <input type="checkbox" :checked="selectedItems.some(i => item.id === i.id)" >
                    </td>

                    <td v-for="column in showedColumns">
                        {{item[column.value]}}
                    </td>

                    <td>
                        <button @click="() => deleteItem(item)">Delete</button>
                    </td>

                </tr>
            </table>

        </div>

    </div>
</template>

<script>
    export default {
        name: "Table",
        props: {
            items: {
                type: Array,
                required: true
            },
            rowsPerPage: {
                type: Number,
                default: 10
            }
        },
        methods: {
            selectRow(item){
                console.log("clicked", item)
                const selected = this.selectedItems.some(i => item.id === i.id)
                if (selected) this.selectedItems = this.selectedItems.filter(i => item.id !== i.id)
                else this.selectedItems.push(item)
            },
            setSortColumn(columnValue) {
                this.sortingValue = columnValue
            },
            selectAllItems(){
                if (this.allItemsAreSelected) this.selectedItems = []
                else this.selectedItems = this.sortedItems
            },
            deleteItem(item){
                if (!confirm("Are you sure you want to delete item?")) return
                this.$emit('deleteItems', [item])

            },
            deleteItems(items){
                if (!confirm(`Are you sure you want to delete ${this.selectedItems.length} items?`)) return
                this.$emit('deleteItems', items)
            }
        },
        async mounted(){
            this.selectedColumns = this.columns
        },
        computed: {
            allItemsAreSelected() {
                return this.selectedItems.length === this.sortedItems.length
            },


            // canPaginateBack(){
            //     return !!this.page
            // },
            // canPaginateForward(){
            //     return this.page*this.rowsPerPage <= this.items.length
            // },
            slicedItems() {
                return this.items.slice(this.rowsPerPage * (this.page), this.rowsPerPage * (this.page + 1))
            },
            sortedItems() {
                return this.slicedItems.sort((a, b) => {
                    if (a[this.sortingValue] > b[this.sortingValue]) {
                        return 1;
                    }
                    if (a[this.sortingValue] < b[this.sortingValue]) {
                        return -1;
                    }
                    return 0
                })
            },

            showedColumns() {
                return this.columns
                    .filter(column => this.selectedColumns.some(hc => hc.value === column.value))
            }
        }


        ,
        data() {
            return {
                selectedItems: [],
                sortingValue: 'product',
                page: 0,
                columns: [
                    {title: 'Product (100g serving)', value: 'product'},
                    {title: 'Calories', value: 'calories'},
                    {title: 'Fat', value: 'fat'},
                    {title: 'Carbs', value: 'carbs'},
                    {title: 'Protein', value: 'protein'},
                ],
                selectedColumns: [],
                rowsPerPageVariants: [
                    10,
                    15,
                    20
                ]
            }
        }
    }

</script>

<style scoped>
    .container {
        width: 90%;
        height: 100%;
        margin: 0 auto;
    }

    .row {
        width: 100%
    }

</style>
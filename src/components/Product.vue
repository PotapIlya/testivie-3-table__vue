<template>
    <tr v-if="items !== null" class="product-row" style="text-align: center">
        <td>
            <button :class="{ 'activeBtn': statusButton }" @click="countSelect(index)">da</button>
        </td>

        <td
            v-for="(item, ind) in items"
            :key="ind"
        >
            {{ item[1] }}
        </td>

        <td>
            <button @click="openModal(index)">Delete</button>
        </td>
        <Modal
            :statusModal="statusModal"

            @closeModal="closeModal"
        />

    </tr>


</template>

<script>
import Modal from "../components/Modal";
    export default
    {
        components: {
            Modal
        },
        props:[
            'product',
            'indexOfTopPanel',
            'index',
            'statusButtons',
            'indexChangeSelectColumnSelected'
        ],
        data: () => ({
            items: null,
            statusModal: false,
            itemId: null,

            statusButton: false,
        }),
        mounted() {
            this.items = Object.entries(this.product);
        },
        watch:{
            indexChangeSelectColumnSelected()
            {
                this.items.splice(this.indexChangeSelectColumnSelected, 1);
            },
            statusButtons()
            {
                this.statusButton = this.statusButtons;
            },
            indexOfTopPanel()
            {
                const first = this.items[this.indexOfTopPanel];

                this.items.splice(this.indexOfTopPanel, 1);
                this.items.unshift(first)

            }
        },
        methods: {
            countSelect(index)
            {
                this.statusButton = !this.statusButton
                this.$emit('countSelect', index)
            },
            closeModal(status)
            {
                this.statusModal = false;
                this.statusButton = false;
                if (status)
                {
                    this.$emit('deleteItemFromArray', this.itemId);
                }
            },
            openModal(id)
            {
                this.itemId = id;
                this.statusModal = true;
                this.statusButton = true;
            }
        }
    }
</script>

<style>
.activeBtn{
    background: green;
    color: #fff;
}
</style>





<!--Route::get('/success/{rand}')->name('success')-->

<!--при создание обратной ссылки на сайт-->
<!--route('success', Str::rand(3) )-->


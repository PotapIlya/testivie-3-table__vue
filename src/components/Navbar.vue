<template>
    <div class="control" style="display: flex">
        <div class="sorting-left" style="display: flex">
            <div
                v-for="(button, index) in arrayNavButtons"
                :key="index"
            >
                <button
                    v-if="button.status"
                    @click="changeButton(index)"
                    type="button"
                >
                    {{button.name}}
                </button>
            </div>
        </div>
        <div class="sorting-right" style="margin-left: 20px; display: flex">
            <button
                @click="deleteObjectCountSelect"
                :disabled="objectCountSelect === 0"
                type="button"
            >
                Delete ({{ objectCountSelect }})
            </button>
            <select v-model.number="amountOnePage">
                <option value="10">10 items</option>
                <option value="15">15 items</option>
                <option value="20">20 items</option>
            </select>

            <div class="pagination" style="margin-left: 20px">
                <button @click="pageNumber--" class="prev" :disabled="pageNumber <= 0">prevPage</button>
                <span> {{ pageNumber }} / {{ countPage }} </span>
                <button @click="pageNumber++" class="next" :disabled="pageNumber >= countPage">nextPage</button>
            </div>

           <div style="position: relative">
               <button @click="showFormColumnSelected = !showFormColumnSelected">{{ countActiveColumnSelected }} column selected </button>
               <ul
                   style="position: absolute; list-style: none"
                   :class="{ 'showForm' : !showFormColumnSelected }" >
                   <li
                       v-for="(item, index) in arrayNavButtons"
                       :key="index"
                   >
                       <span
                           @click="selectColumnSelected(index)"
                           :class="{ 'activeSpan' : item.status }"
                       >
                           {{ item.name }}
                       </span>
                   </li>
               </ul>
           </div>
        </div>
    </div>
</template>

<script>
    export default {
        props: [
            'arrayNavButtons',
            'countArrayProductsLength',
            'objectCountSelect'
        ],
        data: () => ({
            amountOnePage: 10,
            pageNumber: 0,
            countPage: 0,
            showFormColumnSelected: false,

            countActiveColumnSelected: 0,
        }),
        mounted() {
            // для вывода 10 записей
            this.functionAmountOnePage();

            this.countActiveColumnSelect();
        },
        watch: {
            pageNumber()
            {
                this.$emit('changePageNumber', this.pageNumber);
            },
            amountOnePage()
            {
                this.functionAmountOnePage();
            },
            countArrayProductsLength()
            {
                // для слежения при удаление записи
                this.functionAmountOnePage();
            }
        },
        methods:{
            countActiveColumnSelect()
            {
                this.countActiveColumnSelected = 0;

                this.arrayNavButtons.forEach( item => { if (item.status) this.countActiveColumnSelected += 1; })
            },
            selectColumnSelected(index)
            {
                this.$emit('changeSelectColumnSelected', index);

                this.countActiveColumnSelect();
            },
            deleteObjectCountSelect()
            {
                this.$emit('deleteObjectCountSelect')
            },
            functionAmountOnePage()
            {
                // кол-во страниц
                this.countPage = Math.ceil(this.countArrayProductsLength / this.amountOnePage );

                this.$emit('changeAmountOnePage', this.amountOnePage);
            },
            changeButton(id)
            {
                this.$emit('changeArrayNavButtons', id);
            }
        }
    }
</script>

<style>
    .sorting-left div:first-child button{
        background: green;
    }
    .showForm{
        display: none;
    }
</style>


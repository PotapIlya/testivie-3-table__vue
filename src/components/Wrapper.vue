<template>

   <div>
       <div v-if="!loading">
           <h1>LOADING</h1>
       </div>
       <div v-else>
           <Navbar
               :arrayNavButtons="arrayNavButtons"
               :countArrayProductsLength="countArrayProductsLength"
               :objectCountSelect="Object.keys(this.objectCountSelect).length"

               @changeArrayNavButtons="changeArrayNavButtons"
               @changeAmountOnePage="changeAmountOnePage"
               @changePageNumber="changePageNumber"
               @deleteObjectCountSelect="deleteObjectCountSelect"
               @changeSelectColumnSelected="changeSelectColumnSelected"
           />

           <table>

               <thead>
               <tr v-if="arrayNavButtons.length">
                   <td>
                       <button :class="{ 'activeBtn' : statusButtons }" @click="selectAllObjectCountSelect">all</button>
                   </td>

                   <td style="text-align: center"
                       v-for="(btn, index) in arrayNavButtons"
                       :key="index"
                   >
                       <span v-if="btn.status">
                           {{ btn.name }}
                       </span>
                   </td>

               </tr>
               </thead>
               <tbody>
               <Product
                   v-for="(product, index) in shortArrayProducts"

                   :key="product.id"
                   :index="index"

                   :product="product"
                   :indexOfTopPanel="indexOfTopPanel"
                   :statusButtons="statusButtons"

                   :indexChangeSelectColumnSelected="indexChangeSelectColumnSelected"

                   @deleteItemFromArray="deleteItemFromArray"
                   @countSelect="countSelect"
               />
               </tbody>
           </table>
       </div>
   </div>

</template>

<script>
    import Navbar from "./Navbar";
    import Product from "./Product";
    import axios from "axios";

    export default
    {
        components: {
            Navbar,
            Product
        },
        data: () => ({
            url: 'http://localhost:3000/products',
            arrayNavButtons: [
                {name: 'Id', status: true },
                {name: 'Product(100g serving)', status: true},
                {name: 'Calories', status: true},
                {name: 'Fat(g)', status: true},
                {name: 'Carbs(g)', status: true},
                {name: 'Protein(g)', status: true},
                {name: 'Iron(%)', status: true}
            ],
            loading: false,
            countArrayProductsLength: 0,
            amountOnePage: 10,
            start: null,


            indexOfTopPanel: null, // id от верхней панели


            arrayProducts: [],
            shortArrayProducts:[],


            objectCountSelect: {},
            statusButtons: false,


            indexChangeSelectColumnSelected: null, // индекс от выпадающего списка
        }),
        mounted()
        {
            axios.get(this.url)
                .then(res => {
                    this.arrayProducts = res.data;
                    this.countArrayProductsLength = res.data.length;
                    this.loading = true
                })
                .catch(err => {
                    console.error(err);
                })
        },
        methods:{
            changeSelectColumnSelected(index)
            {
                this.indexChangeSelectColumnSelected = index;
                // column selected меняем статус у объекта arrayNavButtons
                !this.arrayNavButtons[index].status ? this.arrayNavButtons[index].status = true : this.arrayNavButtons[index].status = false
            },
            selectAllObjectCountSelect()
            {
                this.shortArrayProducts.forEach(item => {
                    this.objectCountSelect[item.id] = item.id
                })
                this.statusButtons = true;

                // console.log(this.objectCountSelect)
            },
            deleteObjectCountSelect()
            {
                Object.keys(this.objectCountSelect).forEach( id =>
                {
                    console.log( Number(id) )
                    console.log(
                        this.arrayProducts.splice( Number(id) , 1)
                    );
                })


                // this.statusButtons = false;
                this.showItemInPage();
            },
            countSelect(data)
            {
                if (this.objectCountSelect[data]) {
                    delete this.objectCountSelect[data]
                } else{
                    this.objectCountSelect[data] = data
                }
            },
            getCountArrayProductsLength()
            {
                this.countArrayProductsLength = this.arrayProducts.length;
                // console.log(this.countArrayProductsLength)
            },
            deleteItemFromArray(id)
            {
                this.arrayProducts.splice(id, 1)
                this.showItemInPage();
                this.getCountArrayProductsLength();
            },
            changePageNumber(amount)
            {
                this.start = amount * this.amountOnePage;
                let end = this.start + this.amountOnePage;


                // обновить номер страницы
                this.shortArrayProducts = this.arrayProducts.slice(this.start, end)
                this.showItemInPage();

                // console.log(
                //     'start =>'+start,
                //     'end =>'+end,
                //     'amountOnePage =>'+start+ this.amountOnePage,
                // )
            },
            changeAmountOnePage(amount)
            {
                // вывести на странциу "amount" блоков
                this.amountOnePage = amount;
                this.showItemInPage();

                // console.log(
                //     'starzt =>'+this.start,
                //     'end =>'+ (this.start+amount),
                // )
            },
            changeArrayNavButtons(id)
            {
                /**
                 * если 2 раза нажать на одну и ту же кнопку
                 * то id в дате не меняется и не перерендывается компонент
                 */

                // сместить положение кнопкок на верхней панели
                this.indexOfTopPanel = id;
                this.arrayNavButtons.unshift(...this.arrayNavButtons.splice( id,1 ));

                // this.$forceUpdate()
            },
            showItemInPage()
            {
                // обновить кол-во записей
                this.shortArrayProducts = this.arrayProducts.slice(this.start, this.start + this.amountOnePage);
            }
        },
    }
</script>

<style>
    body{
        background: #a1a1a1;
    }
    .activeSpan{
        color: white;
    }

</style>

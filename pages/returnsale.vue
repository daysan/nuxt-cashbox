<template>
<div>
      <div class="container">
          <div class="row">
              <div class="col-md-6">
                <div class="cartList">
                <table class="table">
                    <thead>
                        <tr>
                        <th scope="col">Товар</th>
                        <th scope="col">Цена</th>
                        <th scope="col">Кол-во</th>
                        <th scope="col">Сумма</th>
                        <th scope="col"></th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(item, key) in cart" :key="key">
                        <td>{{item.title}}</td>
                        <td><input type="number" class="form-control count" v-model="item.price"></td>
                        <td><input type="number" class="form-control count" v-model="item.count" v-on:change.prevent="changeCount(item.count, key)"></td>
                        <td>{{item.price*item.count}}₽</td>
                        <td><button type="button" class="btn btn-primary" v-on:click.prevent="delCartItem(key)">X</button></td>
                        </tr>
                    </tbody>
                </table>
                </div>
                  <div class="row">


                    <div class="col-md-6">
                      <label class="form-label">Сумма возврата</label>
                      <input type="text" class="form-control" v-model="count">
                    </div>
                      <div class="col-md-6">
                        <label class="form-label">Способ возврата</label>
                        <select class="form-select form-control" aria-label="Default select example" v-model="payments">
                          <option value="cash" selected>Наличные</option>
                          <option value="electronically">Карта</option>
                      </select>
                    </div>
                      <div class="col-md-6 salebtn">
                      <button type="button" class="btn btn-primary btn-lg">Вернуть</button>
                    </div>
                </div>
              </div>
              <div class="col-md-6">
                  <div class="mb-3">
                      <label class="form-label">Поиск товара</label>
                      <input type="text" class="form-control" v-model="search">
                  </div>
                  <div class="t-scroll">
                    <table class="table">
                      <thead>
                          <tr>
                          <th scope="col">#</th>
                          <th scope="col">Товар</th>
                          <th scope="col">Цена</th>
                          <th scope="col"></th>
                          </tr>
                      </thead>
                      <tbody>
                          <tr v-for="(item, key) in filteredPrice" :key="key">
                          <th scope="row">{{item.id}}</th>
                          <td>{{item.title}}</td>
                          <td>{{item.price}}₽</td>
                          <td><button type="button" class="btn btn-primary" v-on:click.prevent="addCart(item)">Добавить</button></td>
                          </tr>
                      </tbody>
                  </table>
                </div>
              </div>
          </div>
      </div>
</div>
</template>

<script>
import localForage from "localforage"
export default {
    data() {
        return{
            deposit: 0,
            discountSum: 0,
            discount: 'proc',
            payments: 'cash',
            modal: false,
            search: '',
            priceList: [],
            cart: []
        }
    },
    mounted: function() {
        console.log(this.$axios);
        let TH = this
        localForage.getItem('dbProducts').then(data => {
            if(data) {
                TH.priceList = data
            } else {
                TH.priceList = []
            }
        })
    },
    computed: {
        count: function() {
        let sum = 0
        if(this.cart) {
            this.cart.forEach((value, index) => {
            sum += value.price*value.count
            })
        }
        return sum
        },
        countDiscount: function(){
        if(this.depositSum <= 0) {
            return this.count
        }
        if(this.discount == "proc") {
            return this.count-(this.count/100*this.discountSum)
        }
        if(this.discount == 'cash') {
            return this.count-this.discountSum
        }

        },
        filteredPrice: function() {
        return this.priceList.filter(item => {
            // console.log(item);
            // return item
            return item.title.toLowerCase().includes(this.search.toLowerCase())
        })
        },
        cashBack: function() {
        return this.deposit-this.countDiscount
        }
    },
    methods: {
            addCart: function(item) {
            let th = this

            let cartItem = this.cart.find(x => x.id == item.id)
            if(cartItem) {
                cartItem.count++
            } else {
                this.cart.push({
                id: item.id,
                title: item.title,
                price: item.price,
                count: 1
                })
            }
            // console.log(c);
            

            },
            delCartItem: function(key) {
            this.cart.splice(key, 1)
            },
            changeCount: function(count, key) {
                if(count <= 0) {
                    this.cart.splice(key, 1)
                }
            }
    }
}
</script>

<style>

</style>

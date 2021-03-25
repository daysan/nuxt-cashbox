<template>
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
                        <td>{{item.price}}₽</td>
                        <td><input type="number" class="form-control count" v-model="item.count" v-on:change.prevent="changeCount(item.count, key)"></td>
                        <td>{{item.price*item.count}}₽</td>
                        <td><button type="button" class="btn btn-primary" v-on:click.prevent="delCartItem(key)">X</button></td>
                        </tr>
                    </tbody>
                </table>
                </div>
                  <div class="row">
                    <div class="col-md-6">
                       <label class="form-label">Тип скидки</label>
                        <select class="form-select form-control" v-model="discount">
                          <option value="proc">Проценты</option>
                          <option value="cash">Рубли</option>
                      </select>
                    </div>
                    <div class="col-md-6">
                      <label class="form-label">Сумма скидки</label>
                      <input type="text" class="form-control" v-model="discountSum">
                    </div>
                    <div class="col-md-6">
                      <label class="form-label">Сумма без скидки</label>
                      <input type="text" class="form-control" v-model="count" disabled>
                    </div>
                    <div class="col-md-6">
                      <label class="form-label">Сумма со скидкой</label>
                      <input type="text" class="form-control" v-model="countDiscount" disabled>
                    </div>
                    <div class="col-md-6">
                      <label class="form-label">Внесено клиентом</label>
                      <input type="text" class="form-control" v-model="deposit" >
                    </div>
                    <div class="col-md-6">
                      <label class="form-label" v-if="cashBack > 0">Сдача</label>
                      <input type="text" class="form-control" v-model="cashBack" v-if="cashBack > 0" disabled>
                    </div>
                      <div class="col-md-6">
                        <label class="form-label">Способ оплаты</label>
                        <select class="form-select form-control" aria-label="Default select example" v-model="payments">
                          <option value="cash" selected>Наличные</option>
                          <option value="electronically">Карта</option>
                      </select>
                    </div>
                      <div class="col-md-12 salebtn">
                      <button type="button" class="btn btn-primary btn-lg" v-on:click.prevent="saleProd">Продать</button>
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
</template>

<script>
const uuid =()=>([1e7]+-1e3+-4e3+-8e3+-1e11).replace(/[018]/g,c=>(c^crypto.getRandomValues(new Uint8Array(1))[0]&15 >> c/4).toString(16));
import localForage from "localforage"
export default {
  data() {
    return {
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
    // console.log(this);
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
    },
    saleProd: function() {
      let TH = this
      if(!localStorage.getItem('activeSettings')) {
        alert('Обновите настроки программы и нажмите сохранить')
        return false
      }
      if(!this.cart.length) {
        alert('Корзина пуста')
        return false
      }
      let isSale = confirm("Продавть товар(ы)?");
      if(!isSale) {
        return false
      }
      let atolHost = localStorage.getItem('atolHostSetting')
      let products = []
      this.cart.forEach((value, index) => {
        products.push(
          {
                type: "position",
                name: value.title,
                price: value.price,
                quantity: value.count,
                amount: value.price*value.count,
                paymentMethod: "fullPayment",
                paymentObject: "commodity",
                tax: {
                  type: "none"
                }
              },
              {
                type: "text",
                text: "--------------------------------",
                alignment: "left",
                font: 0,
                doubleWidth: false,
                doubleHeight: false
              }
        )
      })
      var atolObj = {
        uuid: uuid(),
        request: [
          {
            type: "sell",
            taxationType: localStorage.getItem('taxationTypeSetting'),
            ignoreNonFiscalPrintErrors: false,
            operator: {
              name: localStorage.getItem('operatorSetting')
            },
            items: products,
            payments: [
              {
                type: this.payments,
                sum: this.count
              }
            ],
            total: this.count
          }
        ]
      };
      // console.log(atolObj);
      let hasPaper = false;
      var getDeviceStatusId = uuid();

      var getDeviceStatus = {
      uuid: getDeviceStatusId,
      request: [
          {
              type: "getDeviceStatus"
          }
      ]};
      this.$axios.post(atolHost+'/requests', JSON.stringify(getDeviceStatus)).then(data => {
        console.log(data);
        
          TH.$axios.get(atolHost+'/requests/'+getDeviceStatusId).then(data => {
          if(!data.data.results[0].result) {
            alert('Атол не подключен к ПК')
            return false
          }
          hasPaper = data.data.results[0].result.deviceStatus.paperPresent
          if(hasPaper) {
             TH.$axios.post(atolHost+'/requests', JSON.stringify(atolObj)).then(data => {
               console.log(data);
             })
          } else {
            alert('В атоле нет бумаги')
            return false
          }
        })
      })
    }
  }
}
</script>

<style>
 .form-control.count {
   width: 75px;
 }
 .modal {
   display: block;
 }
 .t-scroll {
   height: 590px;
   overflow: scroll;
   overflow-x: hidden;
 }
 .cartList {
    height: 400px;
    overflow: scroll;
    overflow-x: hidden;
 }
 .total {
   display: flex;
   justify-content: space-between;
   font-size: 24px;
   font-weight: 600;
   padding: 0 10px;
 }
 .salebtn {
    display: flex;
    flex-flow: column;
    justify-content: center;
    height: 100px;
 }
</style>

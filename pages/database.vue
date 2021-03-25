<template>
<div>
    <div class="container">
        <div class="button-group">
            <button type="button" class="btn btn-primary" v-on:click.prevent="openAddModal">Создать товар</button>
            <!-- <button type="button" class="btn btn-primary" v-if='!csvBtn' v-on:click.prevent="csvBtn = !csvBtn">Импортировать CSV</button>
            <input type="file" class="btn btn-primary" v-if="csvBtn" v-on:change.prevent="uploadFile" /> -->

        </div>
        <div class="row search-group">
            <div class="col-md-6">
                      <label class="form-label">Поиск по артикулу</label>
                      <input type="text" class="form-control" v-model="artFind">
            </div>
            <div class="col-md-6">
                      <label class="form-label">Поиск по товару</label>
                      <input type="text" class="form-control" v-model="prodFind"> 
            </div>

        </div>
        <div class="b-table">
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
            <tr v-for="(item, key) in filterDb" :key="key">
            <th scope="row">{{item.id}}</th>
            <td>{{item.title}}</td>
            <td>{{item.price}}</td>
            <td>
                <button type="button" class="btn btn-primary" v-on:click.prevent="openEditModal(item, key)">Редактировать</button>
                <button type="button" class="btn btn-primary" v-on:click.prevent="removeProduct(key)">Удалить</button>
            </td>
            </tr>
        </tbody>
        
        </table>
        </div>
    </div>
    <div class="modal-dialog addProduct" v-if="addModal">
        <div class="modal-content">
        <div class="modal-header">
            <h5 class="modal-title">Добавление товара</h5>
            <!-- <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close">X</button> -->
        </div>
        <div class="modal-body">
            <div class="mb-3">
                <label class="form-label">Артикул</label>
                <input type="text" class="form-control" v-model="addModalData.id">
            </div>
            <div class="mb-3">
                <label class="form-label">Имя товара</label>
                <input type="text" class="form-control" v-model="addModalData.title">
            </div>
            <div class="mb-3">
                <label class="form-label">Цена</label>
                <input type="number" class="form-control" v-model="addModalData.price">
            </div>
        </div>
        <div class="modal-footer">
            <button type="button" class="btn btn-secondary" v-on:click.prevent="closeAddModal">Отмена</button>
            <button type="button" class="btn btn-primary" v-on:click.prevent="addProduct">Добавить</button>
        </div>
        </div>
  </div>
      <div class="modal-dialog addProduct" v-if="editModal">
        <div class="modal-content">
        <div class="modal-header">
            <h5 class="modal-title">Редактирование товара</h5>
            <!-- <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close">X</button> -->
        </div>
        <div class="modal-body">
            <div class="mb-3">
                <label class="form-label">Акртикул</label>
                <input type="text" class="form-control" v-model="editModalData.id">
            </div>
            <div class="mb-3">
                <label class="form-label">Имя товара</label>
                <input type="text" class="form-control" v-model="editModalData.title">
            </div>
            <div class="mb-3">
                <label class="form-label">Цена</label>
                <input type="number" class="form-control" v-model="editModalData.price">
            </div>
        </div>
        <div class="modal-footer">
            <button type="button" class="btn btn-secondary" v-on:click.prevent="closeEditModal">Отмена</button>
            <button type="button" class="btn btn-primary" v-on:click.prevent="saveProduct">Сохранить</button>
        </div>
        </div>
  </div>
  </div>
</template>

<script>
import localForage from "localforage"
export default {
    data() {
        return {
        editModalData: {
            id: '',
            title: '',
            price: 0

        },
        editModal: false,
        addModalData: {
            key: 0,
            id: '',
            title: '',
            price: 0

        },
        addModal: false,
        csvBtn: false,
        artFind: '',
        prodFind: '',
        products: []
        }
    },
    computed: {
        filterDb: function() {
        if(this.artFind) {
           return this.products.filter(item => {
                return item.id.toLowerCase().includes(this.artFind.toLowerCase())
            })  
        } else if(this.prodFind) {
            return this.products.filter(item => {
                return item.title.toLowerCase().includes(this.prodFind.toLowerCase())
            })
        } else {
            return this.products
        }
      }
    },
    mounted: function() {
    let TH = this
    localForage.getItem('dbProducts').then(data => {
        if(data) {
            TH.products = data
        } else {
            TH.products = []
        }
    })
    },
    methods: {
      openAddModal: function() {
          this.addModal = true
      },
      closeAddModal: function() {
          this.addModal = false
      },
      openEditModal: function(item, key) {
        this.editModalData.key = key
        this.editModalData.id = item.id
        this.editModalData.title = item.title
        this.editModalData.price = item.price
        this.editModal = true
      },
      closeEditModal: function() {
          this.editModal = false
      },
      addProduct: function() {
        let TH = this
        if(!this.addModalData.id) {
            alert('Если вы не введете арткл, то он будет сгенерирован автоматически')
              this.addModalData.id = 'RN'+Math.floor(Math.random() * Math.floor(9999));
          }
        if(!this.addModalData.title) {
              alert('Введите название товара')
              return false
          }
        if(!this.addModalData.price) {
                alert('Введите цену товара')
                return false
        }
        this.products.push({
            id: this.addModalData.id,
            title: this.addModalData.title,
            price: this.addModalData.price
        })
        
        localForage.setItem('dbProducts', TH.products).then(data => {
            // console.log(data);
            this.addModal = false
            this.addModalData.id = ''
            this.addModalData.title = ''
            this.addModalData.price = 0

        })


      },
      saveProduct: function() {
          this.products[this.editModalData.key].id = this.editModalData.id 
          this.products[this.editModalData.key].title = this.editModalData.title 
          this.products[this.editModalData.key].price = this.editModalData.price
          localForage.setItem('dbProducts', this.products).then(data => {
              this.editModal = false
          })
          
          
      },
      removeProduct: function(key) {
          let isRemove = confirm("Вы точно хотите удалить данный товар?");
          if(!isRemove) {
              return false
          }
          this.products.splice(key, 1)
          localForage.setItem('dbProducts', this.products).then(data => {
            // console.log(data);
        })
      }
    }
}
</script>

<style>
.modal-dialog {
    position: absolute;
    width: 500px;;
    top: 50px;
    left: 25vh;
}
.button-group {
    display: flex;
    margin-bottom: 10px;
}
.button-group button {
    margin-right: 10px;
}
.search-group {
    padding-bottom: 15px;
    padding-top: 5px;
    box-shadow: 0px 0px 5px 0px rgb(196 196 196);
    border-radius: 5px;
}
.b-table {
    height: 560px;
    overflow: scroll;
    overflow-x: hidden;
    margin-top: 10px;
}
</style>

<template>
  <div class="products">
    <h3>ChatBot Menú</h3>
    <div class="card">
      <div class="card-header">
        Agregar nuevo menú
      </div>
      <div class="card-body">
        <form class="form-inline" v-on:submit.prevent="onSubmit">
          <div class="form-group">
            <label>Código Padre</label>
            <input v-model="productData.parent_id" type="int" class="form-control ml-sm-2 mr-sm-4 my-2"  required>
          </div>
          <div class="form-group">
            <label>Descripción</label>
            <input v-model="productData.description" type="text" class="form-control ml-sm-2 mr-sm-4 my-2" required>
          </div>
          <div class="form-group">
            <label>Orden</label>
            <input v-model="productData.order" type="int" class="form-control ml-sm-2 mr-sm-4 my-2" required>
          </div>
          <div class="ml-auto text-right">
            <button type="submit" class="btn btn-primary my-2">Agregar</button>
            
          </div>
        </form>
      </div>
    </div>

    <div class="card mt-5">
      <div class="card-header">
        Menú
      </div>
      <div class="card-body">
        <div class="table-responsive">
          <table class="table">
            <thead>
              <tr>
                <th scope="col">
                  Código
                </th>
                <th>
                  Código Padre
                </th>
                <th>
                  Descripción
                </th>
                <th>
                  Orden
                </th>
                <th>
                  &nbsp;
                </th>                
              </tr>
            </thead>
            <tbody>
              <tr v-for="product in sortedProducts" v-bind:key="product.id">
                <template v-if="editId == product.id">
                  <td>{{editProductData.id}}</td>
                  <td><input v-model="editProductData.parent_id" type="text"></td>
                  <td><input v-model="editProductData.description" type="text"></td>
                  <td><input v-model="editProductData.order" type="text"></td>
                  <td>
                    <span class="icon">
                      <i  @click="onEditSubmit(product.id)" class="fa fa-check"></i>
                    </span>
                    <span class="icon">
                      <i  @click="onCancel" class="fa fa-ban"></i>
                    </span>
                  </td>
                </template>
                <template v-else>
                  <td>
                    {{product.id}}
                  </td>
                  <td>
                    {{product.parent_id}}
                  </td>
                  <td>
                    {{product.description}}
                  </td>
                  <td>
                    {{product.order}}
                  </td>                  
                  <td>

                    <a href="#" class="icon">
                      <i v-on:click="onDelete(product.id)" class="fa fa-trash"></i>
                    </a>
                    <a href="#" class="icon">
                      <i v-on:click="onEdit(product)" class="fa fa-pencil"></i>
                    </a>
                    <router-link 
                    :to="{
                      name:'ProductPage', 
                      params:{id: product.id}
                    }" 
                    class="icon"
                    >
                      <i class="fa fa-eye"></i>
                    </router-link>
                  </td>
                </template>
              </tr>

            </tbody>
          </table>
        </div>
      </div>
    </div>

  </div>
</template>

<script>

const axios = require('axios');

export default {
  name: 'Products',
  data () {
    return {
      editId: '',
      productData: {
        'id' : 0,
        'parent_id': 0,
        'description': '',
        'order': 0
      },
      editProductData: {
        'id' : 0,
        'parent_id': 0,
        'description': '',
        'order': 0
      },
      products: []
    }
  },
  created() {
    this.getProducts()
  },
  computed:{
    sortedProducts(){
      return this.products.slice().sort((a,b)=>{
        return a.id - b.id
      })
    }
  },
  methods: {
    async getProducts() {
        await axios.get('https://fnchatbotoptions.azurewebsites.net/api/getMenus?code=UvJGoVtIeWMwMQXgVp4dkzHHjQNQjjDwnQIcdzAWYw0l5rcev19zCA==')
        .then((response) => {
            this.products = response.data;
          })
    },
    
    async onSubmit(){
      await axios.post('https://fnchatbotoptions.azurewebsites.net/api/insertMenu?code=1PqccjtxPncGsvSz6oMbpaMZM2sYGzoaTJOpKMgoZEyxqtvkEdv4hw==', JSON.stringify(this.productData))
        .then((response) => {
            this.getProducts()
          })      
      this.productData.parent_id = 0
      this.productData.description = ''
      this.productData.order = 0

    },
    async onDelete(id){
      await axios.get('https://fnchatbotoptions.azurewebsites.net/api/deleteMenuById?code=raUqHWG2r4nJBmcHRA/6dQZKgK1iyvJ0UZK8f8R9l1uaswO56ckk9g==&id='+id)
        .then((response) => {
            this.getProducts()
          })  
    },
    onEdit(product){
      this.editId = product.id
      this.editProductData.id = product.id
      this.editProductData.parent_id = product.parent_id
      this.editProductData.description = product.description
      this.editProductData.order = product.order
    },
    onCancel(){
      this.editId = 0
      this.editProductData.parent_id = 0
      this.editProductData.description = ''
      this.editProductData.order = 0
    },
    async onEditSubmit (id){
      await axios.post('https://fnchatbotoptions.azurewebsites.net/api/updateMenu?code=MQvrolue/9IfzzGSWbRlo2fIdIuZBqH9JaV9dMBxcQydkZfG6gkLkA==',JSON.stringify(this.editProductData))
        .then((response) => {
            this.getProducts()
          })  
        this.editId = 0
        this.editProductData.parent_id = 0
        this.editProductData.description = ''
        this.editProductData.order = 0
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3{
  text-align: center;
  margin-top: 30px;
  margin-bottom: 20px;
}
.icon{
  margin-right: 10px;
}
.icon i{
  cursor: pointer;
}
</style>

<template>
  <q-page class="flex flex-center container">
    <span style="font-size: 32px; padding: 20px">Shopping do Chocolate</span>
    <div class="container--products" style="padding: 0px 1px 0 1px; margin-left: 20px">
      <div v-for="(item, i) in products" :key="i" class="container--box" style="cursor:pointer" @click="addProduct(i, item)">
        <img :src="item.imageUrl" style="width: 128px; height: 128px"/>
        <div class="container--box--title">
          <q-text class="lower">{{ item.name }}</q-text>
          <q-text class="subprice">R$ {{ (item.sellingPrice/validator).toString().replace('.', ',') }}</q-text>
          <q-text lass="price">R$ {{ (item.price/validator).toString().replace('.', ',') }}</q-text>
        </div>
      </div>
    </div>
    <q-card class="basket" :style=" totalprice > 1000 ? 'height: 840px;' : 'height: 799px;' ">
      <span class="basket-content">
        <q-text style="font-weight: 700">Meu carrinho</q-text>
      </span>
      <q-separator></q-separator>
      <div class="basket-scroll" style="overflow-y: scroll; height: 586px">
        <div v-for="(item, i) in basket" :key="i" class="container--box" style="cursor:pointer" @click="remove(i, item)">
          <img :src="item.imageUrl" style="width: 128px; height: 128px"/>
          <div class="container--box--title">
            <q-text class="lower">{{ item.name }}</q-text>
            <q-text class="subprice">R$ {{ (item.sellingPrice/validator).toString().replace('.', ',') }}</q-text>
            <q-text class="price">R$ {{ (item.price/validator).toString().replace('.', ',') }}</q-text>
          </div>
        </div>
      </div>
      <q-separator></q-separator>
      <span class="total">
        <q-text style="font-weight: 700">Total: </q-text>
        <q-text style="font-weight: 700">R$ {{(totalprice/validator).toString().replace('.', ',')}}</q-text>
      </span>
      <div v-if="totalprice > 1000" class="banner">
        <span >Parabéns, sua compra tem frete grátis !</span>
      </div>
      <q-separator></q-separator>
      <span class="content">
        <q-btn class="content--button" flat style="font-weight: 700" @click="concluded = true">Finalizar compra</q-btn>
      </span>
    </q-card>
    <q-dialog v-model="modal" persistent>
      <q-card>
        <q-card-section class="row items-center">
          <span class="q-ml-sm">Teste desenvolvido para oportunidade de Front end no Codeby</span>
          <span class="q-ml-sm">Na tela principal encontramos os produtos dispostos na parte de cima, ao clicar em cada produto adicionamos na cesta de compras. Todos elementos inseridos na cesta de compras são contabilizados e somados os valores finais. Caso queira remover algum item, é só clicar no mesmo.</span>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat @click="modal = false" label="Fechar" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog><q-dialog v-model="concluded" persistent>
      <q-card>
        <q-card-section class="row items-center">
          <span class="q-ml-sm">Obrigado pela oportunidade de participar do processo seletivo!</span>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat @click="modal = false" label="Fechar" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import axios from 'axios'
export default {
  name: 'PageIndex',
  data () {
    return {
      products: [],
      basket: [],
      modal: false,
      validator: 100,
      concluded: false
    }
  },
  created () {
    this.getData()
    this.getDatas()
    this.modal = true
  },
  computed: {
    totalprice () {
      const arrayOrder = this.basket
      console.log(this.basket)
      return arrayOrder.reduce((acc, item) => {
        return acc + item.price
      }, 0)
    },
  },
  methods: {
    formatCurrency (val) {
      let num = val.replace('.', ',')
    },
    getDatas () {
      try {
        axios.get('https://s3.us-west-2.amazonaws.com/secure.notion-static.com/11b895d0-bc64-4f3a-bfa9-7c652be8d415/acima-10-reais.json?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220607%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220607T201040Z&X-Amz-Expires=86400&X-Amz-Signature=f6b6848c566033fe1b4d32303d71711fa09b3cbac8a09cb93171b97c69c9623b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22acima-10-reais.json%22&x-id=GetObject')
        .then((response) => {
          this.products = response.data.items
          console.log('data', this.products)
        })
        console.log()
      }catch (e) {
        console.error(e)
      }
    },
    getData () {
      try {
        axios.get('https://s3.us-west-2.amazonaws.com/secure.notion-static.com/5bbd6fdd-abae-411d-96cc-1a5d76d3803b/abaixo-10-reais.json?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220607%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220607T191942Z&X-Amz-Expires=86400&X-Amz-Signature=cbb02b678de546029ed54ce50d49c31975e4147b60daad7819f6fe6710f45a00&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22abaixo-10-reais.json%22&x-id=GetObject')
        .then((response) => {
          this.products = response.data.items
          console.log('data', this.products)
        })
        console.log()
      }catch (e) {
        console.error(e)
      }
    },
    addProduct (i, item) {
      console.log('i', i)
      console.log('item', item)
      this.basket.push(item)
    },
    remove (i, item) {
      console.log('i', i)
      console.log('item', item)
      this.basket.splice(i, 1)
    }
  }
}
</script>
<style scoped lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300&display=swap');

.subprice {
  font-size: 12px;
  color: grey
}

.price {
  color: black;
  font-weight: bold;
}

.lower {
  text-transform: lowercase;
}

.banner {
  span {
    background: rgb(109, 214, 109);
    padding: 2px 50px;
    border-radius: 50px;
    border: 2px solid rgb(39, 185, 9);
  }
}

.container {
  display: flex;
  flex-direction: column;
  margin: 10px;
  &--products {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    margin-bottom: 100px;
    margin-top: 50px;
  }
  &--box {
    display: flex;
    align-items: center;
    gap: 20px;
    padding: 10px 20px;
    &--title {
      display: flex;
      flex-direction: column;
    }
    img {
      padding: 20px;
      border: 1px solid #e5e5e5;
    }
  }
}
.content {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 15px 0;
  &--button {
    width: 92%;
    height: 60px;
    background: rgb(22, 77, 180);
    color: white;
  }
}

.basket {
  padding: 0px 1px 0 1px; width: 500px; box-shadow: grey 2px 0px 150px; border-radius: 10px
}

.basket-content, .banner {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 14px;
  font-family: 'Poppins', sans-serif;
}
.total {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
}

.basket-scroll::-webkit-scrollbar-track
{
	-webkit-box-shadow: inset 0 0 2px rgba(0,0,0,0.3);
	border-radius: 10px;
	background-color: #F5F5F5;
}

.basket-scroll::-webkit-scrollbar
{
	width: 3px;
	background-color: #F5F5F5;
}

.basket-scroll::-webkit-scrollbar-thumb
{
	border-radius: 10px;
	-webkit-box-shadow: inset 0 0 2px rgba(0,0,0,.3);
	background-color: grey;
}

@media only screen and (max-width: 768px) {
  .container {
    &--products {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
    }
  }
}

@media only screen and (max-width: 425px) {
  .basket {
    padding: 0px 1px 0 1px;
    width: 100%;
  }
  .container {
    &--products {
      display: grid;
      grid-template-columns: repeat(1, 1fr);
    }
  }
}
</style>

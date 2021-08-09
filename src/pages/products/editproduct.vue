<template>
  <div class="col-12 grid-margin" >
    <div class="col-md-6 grid-margin stretch-card">
      <div class="card">
        <div class="card-body">
          <h4 class="card-title">Edição do produto {{name}}</h4>
          <form class="forms-sample">
            <b-form-group  label="Nome" description="Nome do produto" >
              <b-form-input v-model="name" type="text"></b-form-input>
            </b-form-group>
            <b-form-group label="Nome de impressão" description="Nome de impressão" label-for="imp_name">
              <b-form-input v-model="imp_name" type="text" id="imp_name"></b-form-input>
            </b-form-group>
            <b-form-group label="Categoria" description="Categoria do produto">
              <b-form-select class="text-white" v-model="selectedCategory" :options="category" placeholder="Escolhe a categoria" value="null" />
            </b-form-group>
            <b-form-group label="Tipo" description="Tipo do produto">
              <b-form-select class="text-white" v-model="selectedType" :options="typeProduct" placeholder="Escolha o tipo" value="null"/>
            </b-form-group>
            <b-form-group label="Preço" description="Preço" label-for="selling_price">
              <b-form-input v-model="selling_price" type="number" id="selling_price"></b-form-input>
            </b-form-group>
            <b-form-group label="Medida" description="Medida">
              <b-form-select class="text-white" v-model="selectedMeasure" :options="measure" placeholder="Escolha a medida"  value="null"/>
            </b-form-group>
            <b-form-group label="Unidade de medida" description="Unidade de medida" label-for="measure_qtt">
              <b-form-input v-model="measure_qtt" type="number" id="measure_qtt"></b-form-input>
            </b-form-group>
            <div class="form-check form-check-success" style="padding-right: 10px;">
                <label class="form-check-label">
                <input type="checkbox" class="form-check-input" v-model="active" checked id="active"/>
                Ativo?  
                <i class="input-helper"></i>
                </label>
              </div>
              <div class="form-check form-check-success" >
                <label class="form-check-label">
                <input type="checkbox" v-model="comission" class="form-check-input" id="comission"/>
                Serviço?
                <i class="input-helper"></i>
                </label>
              </div>
          </form>
          <b-button variant="light" class="btn btn-fw" @click="createProduct">Atualizar</b-button>
        </div>
    </div>
  </div>
  </div> 
</template>

<script>
import axios from 'axios'
import Swal from 'sweetalert2'

export default {
  name: "editproduct",
  props: ['prodObj'],
  data() {
    return {
      category: [{ value: null, text: 'Selecione uma opção' },{ value: 1, text: 'Bebidas' }, { value: 2, text: 'Almoço'}, { value: 3, text: 'Jantar' },],
      typeProduct: [{ value: null, text: 'Selecione uma opção' },{ value: 1, text: 'Produto' }, { value: 2, text: 'Serviço'}, { value: 3, text: 'Insumo' },],
      measure: [{ value: null, text: 'Selecione uma opção' },{ value: 1, text: 'UN' }, { value: 2, text: 'KG'}, { value: 3, text: 'LT' },],
      selectedCategory: this.prodObj.categoryId,
      selectedType: this.prodObj.typeId,
      selectedMeasure: this.prodObj.measureId,
      name: this.prodObj.name,
      imp_name: this.prodObj.impName,
      selling_price: this.prodObj.sellingPrice,
      measure_qtt: this.prodObj.measureQtt,
      active: this.prodObj.active,
      comission: this.prodObj.comission
    };
  },
  methods: {
    createProduct: function (){
      if(this.active){
        this.active = 1
      }else{
        this.active = 0
      }
      if(this.comission){
        this.comission = 1
      }else{
        this.comission = 0
      }
      var product = {
        id: this.prodObj.id,
				name: this.name,
				impName: this.imp_name,
				categoryId: this.selectedCategory,
				typeId: this.selectedType,
				buyingPrice: this.buying_price,
        sellingPrice: this.selling_price,
        selectedMeasure: this.selectedMeasure,
        measure_qtt: this.measure_qtt,
        measureId: this.selectedMeasure,
        active: this.active,
        comission: this.comission
      }
      /* eslint-disable no-console */
      axios.put("http://localhost:8081/products?id=" + product.id, product)
      this.$emit('resetModal')
      Swal.fire({
          position: 'top-end',
          type: 'success',
          title: 'Produto atualizado com sucesso',
          showConfirmButton: false,
          timer: 1500
        })
        
    }
  }
}
</script>
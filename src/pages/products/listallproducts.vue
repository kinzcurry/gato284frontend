<template>
<section class="tables">
    <div class="row">
      <div class="col-lg-12 grid-margin stretch-card">
        <div class="card">
          <div class="card-body">
            <h4 class="card-title ">Tabela de produtos</h4>
			<b-col lg="6" class="my-1">
			<b-form-group label="Filtro" label-for="filter-input" label-cols-sm="3"	label-align-sm="right" label-size="sm" class="mb-0">
				<b-input-group size="sm">
					<b-form-input id="filter-input"	v-model="filter" type="search" placeholder="Pesquisar"></b-form-input>
					<b-input-group-append>
						<b-button :disabled="!filter" @click="filter = ''">Limpar</b-button>
					</b-input-group-append>
				</b-input-group>
			</b-form-group>
			</b-col>
            <b-table :filter="filter" :filter-included-fields="filterOn" @filtered="onFiltered" :items="allProducts" responsive :per-page="perPage" :current-page="currentPage" :fields="fields" :sort-by.sync="sortBy" hover dark :sort-desc.sync="sortDesc">
              <template v-slot:cell(editar)="row">
				<button class="btn btn-outline-primary px-4 py-1" v-b-modal.modalEdit @click="fillObj(row.item)" ><i class="ti-eye text-primary mr-2"></i>Editar</button>
              </template>
              <template v-slot:cell(deletar)="row">
				<alert-prompt :text="'produto'" v-on:deletar="deleteProduct(row.item.id)"/>
              </template>
            </b-table>
            <b-pagination v-model="currentPage" :total-rows="rows" :per-page="perPage" aria-controls="table-list" align="right"></b-pagination>
          </div>
			<b-modal
			id="modalEdit"
			ref="modal"
			title="Edite o produto"
			size="xl"
			ok-title="Atualizar"
			@show="resetModal"
			@hidden="resetModal"
			@ok="handleOk">
			
			<editproduct :prodObj="obj" style="width: 100%; margin-left: 200px;"/>	
			</b-modal>
        </div>
      </div>
    </div>
    <div></div>
  </section>
</template>

<script>
import axios from 'axios'
import alertPrompt from "../../components/alerts/sweet-alert/alertPrompt";
import editproduct from "./editproduct.vue"

export default {
	name: "listallproducts",
	data (){
		return {
			allProducts: [],
			//Produto para update
			prod: [],
			prodName: '',
			prodId: '',
			prodImpName: '',
			prodCat: '',
			prodType: '',
			prodMeasure: '',
			prodMeasureQ: '',
			prodPrice: 0,
			prodActive: '',
			prodComission: '',
			obj: [],
			//outras coisas
			fields: [
				{
					key: 'id',
					label: 'id',
					sortable: true,
				},
				{
					key: 'name',
					label: 'Nome',
					sortable: true,
				},
				{
					key: 'impName',
					label: 'Nome de impressão'
				},
				{
					key: 'sellingPrice',
					label: 'Preço',
					formatter: value => {
						return value.toLocaleString('pt-br',{style: 'currency', currency: 'BRL'})
					},
					sortable: true,
				},
				{
					key: 'categoryId',
					label: 'Categoria',
					sortable: true,
				},
				{
					key: 'typeId',
					label: 'Tipo'
				},
				{
					key: 'active',
					label: 'Ativo?',
					formatter: value => {
						var activeValue = ''
						if(value == 1){
							activeValue = 'Sim'
							return activeValue
						}else{
							activeValue = 'Não'
							return activeValue
						}
					}
				},
				{
					key: 'comission',
					label: 'Comissão?',
					formatter: value => {
						var activeValue = ''
						if(value == 1){
							activeValue = 'Sim'
							return activeValue
						}else{
							activeValue = 'Não'
							return activeValue
						}
					}
				},
				{
					key: 'measureId',
					label: 'Medida'
				},
				{
					key: 'measureQtt',
					label: 'Unidade de medida'
				},
				{
					key: 'editar',
					label: 'Editar'
				},
				{
					key: 'deletar',
					label: 'Deletar'
				},
			],
			sortBy: "id",
			perPage: 20,
			currentPage: 1,
			sortDesc: false,
			sortByFormatted: true,
			filterByFormatted: true,
			sortable: true,
			filter: null,
			filterOn: [],
			prodObj: []
		}
	},
	methods: {
		listall: function (){
			axios.get("http://localhost:8081/products").then(response => {
				this.allProducts = response.data
			})
		},
		onFiltered(filteredItems) {
			// Trigger pagination to update the number of buttons/pages due to filtering
			this.totalRows = filteredItems.length
			this.currentPage = 1
		},
		deleteProduct: function(id) {
			axios.delete("http://localhost:8081/products?id="+id).then( 
				this.listall()
			)		
		},
		changeParameters: function(item){
			/* eslint-disable no-console */
			this.prod = item
			console.log(this.prodName)
			this.prodName = item.name
			this.prodId = item.id
			this.prodImpName = item.impName
			this.prodCat = item.categoryId
			this.prodType = item.typeId
			this.prodComission = item.comission
			this.prodActive = item.active
			this.prodPrice = item.sellingPrice
			this.prodMeasure = item.measureId
			this.prodMeasureQ = item.measureQtt
		},
		resetModal: function(){
			this.modalAppears = false
		},
		handleOk: function(){
			/* eslint-disable no-console */
			this.prod.name = this.prodName
			this.prod.impName = this.prodImpName
			this.prod.categoryId = this.prodCat
			this.prod.typeId = this.prodType
			this.prod.comission = this.prodComission
			this.prod.active = this.prodActive
			this.prod.sellingPrice = this.prodPrice
			this.prod.measureId = this.prodMeasure
			this.prod.measureQtt = this.prodMeasureQ
			axios.put("http://localhost:8081/products?id=" + this.prodId, this.prod)
		},
		fillObj: function(rowItem){
			this.obj = rowItem
			this.$bvModal.show('modalEdit')
		}

	},
	mounted: function (){
		this.listall()
	},
	computed: {
		rows() {
			return this.allProducts.length;
		}
	},
	watch: {
		allProducts: function (){
			this.listall()
		}
	},
	components: {
		alertPrompt,
		editproduct
	}
}
</script>
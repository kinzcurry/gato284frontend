<template>
	<div class="col-12 grid-margin" >
		<div class="col-md-12 grid-margin stretch-card">
			<div class="card">
				<div class="card-body">
					<h4 class="card-title">Cadastro de categorias</h4>
					<form class="forms-sample">
						<b-form-group  label="Nome" description="Nome Categoria" >
							<b-form-input v-model="name" type="text"></b-form-input>
						</b-form-group>
						<b-form-group label="Categoria pai" description="Categoria pai">
							<b-form-select class="text-white" v-model="selectedCategory" :options="category" placeholder="Escolhe a categoria pai" value="null" />
						</b-form-group>
						<b-form-group label="Cor de exibicao">
							<v-swatches v-model="color" 
							popover-x="right" 
							swatches="text-advanced" 
							show-fallback 
							fallback-input-type="color">
							</v-swatches>
						</b-form-group>
						<b-button variant="light" class="btn btn-fw" @click="createCategory">Cadastrar</b-button>
						<button type="button" class="btn btn-outline-light btn-fw" @click="cancel">Limpar</button>
					</form>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import axios from 'axios'
import Swal from 'sweetalert2'
import VSwatches from 'vue-swatches'
import 'vue-swatches/dist/vue-swatches.css'

export default {
	data (){
		return {
			name: '',
			allCategory: [],
			category: [],
			selectedCategory: null,
			color: ''
		}
	},
	components:{
		VSwatches,
	},
	methods: {
		listAllCategorys: function(){
			axios.get("http://localhost:8081/category").then(response => {
			/* eslint-disable no-console */
			this.allCategory = response.data
			for(var cat in this.allCategory){
				this.category.push(this.allCategory[cat].name)
			}
			})
		},
		createCategory: function(){
			var category = {
				name: this.name,
				parentId: this.searchParent(),
				color: this.color,
				level: this.searchLevel()
			}
			axios.post("http://localhost:8081/category", category)
			Swal.fire({
				position: 'top-end',
				type: 'success',
				title: 'Categoria cadastrado com sucesso',
				showConfirmButton: false,
				timer: 1500
			})
			this.cancel()
		},
		searchLevel: function(){
			for(var cat in this.allCategory){
				if(this.allCategory[cat].name == this.selectedCategory){
					/* eslint-disable no-console */
					console.log(this.allCategory[cat].level)
					return this.allCategory[cat].level + 1
				}
			}
		},
		searchParent: function(){
			for(var cat in this.allCategory){
				if(this.allCategory[cat].name == this.selectedCategory){
					return this.allCategory[cat].id
				}
			}
		},
		cancel: function(){
			this.name = '',
			this.selectedCategory = null,
			this.color = null,
			this.allCategory = []
		}
	},
	mounted: function (){
		this.listAllCategorys()
	}
}
</script>
<template>
  <section class="alert-prompt">
    <button class="btn btn-outline-danger" v-on:click="showAlert">Deletar</button>
  </section>
</template>

<script>
export default {
  name: 'alert-prompt',
  props: ['text'],
  methods: {
    showAlert () {
      this.$swal({
        title: "Deseja deletar o " + this.text + "?",
        text: "Esse comando não poderá ser desfeito",
        type: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Sim, delete!',
        cancelButtonText: 'Cancelar'
      }).then((result) => {
        if (result.value) {
          this.$swal(
            'Feito!',
            'Deletado com sucesso',
            'success'
          )
          this.$emit('deletar')
        }
      })
    }
  }
}
</script>

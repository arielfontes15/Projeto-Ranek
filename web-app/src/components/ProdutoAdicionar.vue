<template>
  <form class="adicionar-produto">
    <label for="nome">Nome</label>
    <input type="text" id="nome" name="nome" v-model="produto.nome" />

    <label for="preco">Preço (R$)</label>
    <input type="number" id="preco" name="preco" v-model="produto.preco" />

    <label for="fotos">Fotos</label>
    <input type="file" id="fotos" name="fotos" ref="fotos" />

    <label for="descricao">Descrição</label>
    <textarea id="descricao" name="descricao" v-model="produto.descricao" />

    <input
      class="btn"
      type="button"
      value="Adicionar Produto"
      @click="adicionarProduto"
    />
  </form>
</template>

<script>
import { api } from "@/services.js";

export default {
  name: "ProdutoAdicionar",
  data() {
    return {
      produto: {
        nome: "",
        preco: 0,
        descricao: "",
        fotos: null,
        vendido: "false"
      },
    };
  },
  methods: {
    formatarProduto() {
      this.produto.usuario_id = this.$store.state.usuario.id;
    },
    adicionarProduto() {
      this.formatarProduto();
      api.post("/produto", this.produto).then(() => {
        this.$store.dispatch("getUsuarioProdutos");
      });
    },
  },
};
</script>

<style scoped>
.adicionar-produto {
  display: grid;
  grid-template-columns: 100px 1f;
  align-items: center;
  margin-bottom: 60px;
}

.btn {
  grid-column: 2;
}
</style>

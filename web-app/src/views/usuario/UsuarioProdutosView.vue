<template>
  <section>
    <h2>Adicionar Produto</h2>
    <ProdutoAdicionar />
    <h2>Seus Produtos</h2>
    <transition-group v-if="usuario_produtos" name="list" tag="ul">
      <li v-for="produto in usuario_produtos" :key="produto.id">
        <ProdutoItem :propProduto="produto">
          <p>{{ produto.descricao }}</p>
          <button class="deletar" @click="deletarProduto(produto.id)">
            Deletar
          </button>
        </ProdutoItem>
      </li>
    </transition-group>
  </section>
</template>

<script>
import { mapState, mapActions } from "vuex";
import { api } from "@/services.js";

export default {
  name: "UsuarioProdutosView",
  components: {
    ProdutoAdicionar: () => import("@/components/ProdutoAdicionar.vue"),
    ProdutoItem: () => import("@/components/ProdutoItem.vue"),
  },
  computed: {
    ...mapState(["login", "usuario", "usuario_produtos"]),
  },
  methods: {
    ...mapActions(["getUsuarioProdutos"]),
    deletarProduto(id) {
      const confirmar = window.confirm(
        "Voce deseja realmente remover este produto?"
      );
      if (confirmar) {
        api
          .delete(`/produto/${id}`)
          .then(() => {
            this.getUsuarioProdutos();
          })
          .catch((err) => {
            console.log(err);
          });
      }
    },
  },
  watch: {
    login() {
      this.getUsuarioProdutos();
    },
  },
  created() {
    if (this.login) {
      this.getUsuarioProdutos();
    }
  },
};
</script>

<style scoped>
.produto {
  box-shadow: 0 4px 8px rgba(30, 60, 90, 0.4);
  padding: 10px;
  background: white;
  border-radius: 10px;
  transition: all 0.2s;
}

h2 {
  margin-bottom: 20px;
}

.list-enter,
.list-leave-to {
  opacity: 0;
  transform: translate3d(20px, 0, 0);
}

.list-enter-active,
.list-leave-active {
  transition: all 0.3s;
}

.deletar {
  position: absolute;
  top: 0px;
  right: 0px;
  background: url("../../assets/remove.svg") no-repeat center center;
  width: 24px;
  height: 24px;
  text-indent: -140px;
  overflow: hidden;
  cursor: pointer;
  border: none;
}
</style>

<template>
  <section class="produtos-container">
    <transition mode="out-in">
      <div v-if="produtos && produtos.length" class="produtos" key="produtos">
        <div class="produto" v-for="produto in produtos" :key="produto.id">
          <router-link :to="{name: 'produto', params: {id: produto.id}}">
            <img
              v-if="produto.fotos"
              :src="produto.fotos[0].src"
              alt="produto.fotos[0].titulo"
            />
            <p class="preco">{{ produto.preco | formatPreco}}</p>
            <h2 class="titulo">{{ produto.nome }}</h2>
            <p>{{ produto.descricao }}</p>
          </router-link>
        </div>

        <ProdutosPaginar
          :produtosTotal="produtosTotal"
          :produtosPorPagina="produtosPorPagina"
        />
      </div>
      <div v-else-if="produtos && produtos.length === 0" key="sem-resultados">
        <p class="sem-resultados">
          Busca sem resultados. Tente buscar outro produto.
        </p>
      </div>
      <PaginaCarregando v-else key="carregando" />
    </transition>
  </section>
</template>

<script>
import { api } from "@/services.js";
import { serialize } from "@/helpers.js";

export default {
  name: "ProdutosLista",
  data() {
    return {
      produtos: null,
      produtosPorPagina: 3,
      produtosTotal: 0,
    };
  },
  components: {
    ProdutosPaginar: () => import("@/components/ProdutosPaginar.vue"),
  },
  computed: {
    url() {
      const query = serialize(this.$route.query);
      return `/produto?_limit=${this.produtosPorPagina}${query}`;
    },
  },
  watch: {
    url() {
      this.getProdutos();
    },
  },
  methods: {
    getProdutos() {
      this.produtos = null;
      api.get(this.url).then((response) => {
        window.setTimeout(() => {
          this.produtosTotal = Number(response.headers["x-total-count"]);
          this.produtos = response.data;
        }, 1500);
      });
    },
  },
  created() {
    this.getProdutos();
  },
};
</script>

<style scoped>
.produtos-container {
  max-width: 1000px;
  margin: 0 auto;
}

.produtos {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 30px;
  margin: 30px;
}

.produto {
  box-shadow: 0 4px 8px rgba(30, 60, 90, 0.1);
  padding: 10px;
  background: white;
  border-radius: 4px;
  transition: all 0.2s;
}

.produto:hover {
  box-shadow: 0 6px 12px rgba(30, 60, 90, 0.2);
  transform: scale(1.1);
  position: relative;
  z-index: 1;
}

.produto img {
  border-radius: 4px;
  margin-bottom: 20px;
}

.titulo {
  margin-bottom: 10px;
}

.preco {
  font-weight: bold;
  color: #e80;
}

.sem-resultados {
  text-align: center;
}
</style>

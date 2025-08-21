<template>
  <main :class="['page', pageClass]">
    <section class="card">
      <header class="card__header">
        <h1>E-Commerce Catalog</h1>
        <p class="subtitle">Product #{{ productIndex }}</p>
      </header>

      <div v-if="loading" class="state state--loading">Loading...</div>

      <div v-else-if="isUnavailable" class="state state--unavailable">
        <h2>Unavailable product</h2>
        <p>Category: {{ lastCategory || 'Unknown' }}</p>
        <img v-if="lastImage" :src="lastImage" alt="placeholder" class="img--placeholder" />
      </div>

      <article v-else class="product">
        <img :src="product.image" :alt="product.title" class="product__image" />
        <div class="product__info">
          <h2 class="product__title">{{ product.title }}</h2>
          <p class="product__category">{{ product.category }}</p>
          <p class="product__price">${{ product.price }}</p>
          <p class="product__desc">{{ product.description }}</p>
        </div>
      </article>

      <footer class="card__footer">
        <button @click="nextProduct" class="btn">Next Product</button>
      </footer>
    </section>
  </main>
</template>

<script>
export default {
  name: 'ProductViewer',
  data() {
    return {
      productIndex: 1,
      product: null,
      isUnavailable: false,
      loading: false,
      lastCategory: '',
      lastImage: ''
    }
  },
  computed: {
    pageClass() {
      if (this.isUnavailable) return 'page-unavailable'
      if (!this.product) return ''
      if (this.product.category === "men's clothing") return 'page-men'
      if (this.product.category === "women's clothing") return 'page-women'
      return 'page-unavailable'
    }
  },
  created() {
    this.fetchProduct()
  },
  methods: {
    nextProduct() {
      this.productIndex = this.productIndex >= 20 ? 1 : this.productIndex + 1
      this.fetchProduct()
    },
    async fetchProduct() {
      this.loading = true
      this.isUnavailable = false
      this.product = null
      try {
        const res = await fetch(`https://fakestoreapi.com/products/${this.productIndex}`)
        if (!res.ok) throw new Error('Network response was not ok')
        const data = await res.json()
        this.lastCategory = data.category || ''
        this.lastImage = data.image || ''
        if (data.category === "men's clothing" || data.category === "women's clothing") {
          this.product = data
        } else {
          this.isUnavailable = true
        }
      } catch (err) {
        console.error(err)
        this.isUnavailable = true
        this.lastCategory = 'Error'
      } finally {
        this.loading = false
      }
    }
  }
}
</script>

<style scoped>
/* Styling kecil di sini jika perlu; gaya utama ada di assets/styles.css */
</style>

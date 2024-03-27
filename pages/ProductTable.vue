<template>
  <div>
    <h1>Список продуктів</h1>

    <div>
      <!-- Пошук -->
      <input type="text" v-model="searchQuery" placeholder="Пошук продукта" style="margin-bottom: 10px; color:white;">

      <!-- Таблиця з продуктами -->
      <table>
        <thead>
        <tr>
          <th>Назва</th>
          <th>Опис</th>
          <th>Ціна</th>
          <th>Оцінка</th>
          <th>Бренд</th>
          <th>Категорія</th>
          <th>Фото</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="product in paginatedProducts" :key="product.id">
          <td>{{ product.title }}</td>
          <td>{{ product.description }}</td>
          <td>{{ product.price }}</td>
          <td :style="{ color: product.rating < 4.5 ? 'red' : 'green' }">{{ product.rating }}</td>
          <td>{{ product.brand }}</td>
          <td>{{ product.category }}</td>
          <td><img :src="product.thumbnail" alt="Product Thumbnail" style="width: 100px; height: 100px;"></td>
        </tr>
        </tbody>
      </table>
    </div>

    <!-- Пагінація -->
    <button @click="previousPage" :disabled="currentPage === 1">Назад</button>
    <span>Сторінка {{ currentPage }} з {{ totalPages }}</span>
    <button @click="nextPage" :disabled="currentPage === totalPages">Вперед</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      products: [], // Список продуктів
      searchQuery: '', // Пошуковий запит
      currentPage: 1, // Поточна сторінка
      itemsPerPage: 3
      // Кількість елементів на сторінці
    };
  },
  computed: {
    // Обчислення загальної кількості сторінок
    totalPages() {
      return Math.ceil(this.filteredProducts.length / this.itemsPerPage);
    },
    // Фільтрація продуктів за пошуковим запитом
    filteredProducts() {
      return this.products.filter(product =>
        product.title.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
        product.description.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
        product.brand.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
        product.category.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },

    // Відфільтрований та з пагінацією список продуктів для поточної сторінки
    paginatedProducts() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.filteredProducts.slice(startIndex, endIndex);
    }
  },
  methods: {
    // Перехід на попередню сторінку
    previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
    // Перехід на наступну сторінку
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    async fetchProducts() {
      try {
        const response = await axios.get('https://dummyjson.com/products');
        this.products = response.data.products;
      } catch (error) {
        console.error('Error fetching products:', error);
      }
    }
  },
  mounted() {
    this.fetchProducts();
  }
};
</script>

<style>
table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  color: black;
  background-color: #f2f2f2;
}

/* Зміна кольору рядків при наведенні миші */
tr:hover {
  background-color: lightgray;
  color: black;
}
/* Стилі для кнопок */
button {
  width: 90px; /* фіксована ширина кнопки */
  height: 30px; /* фіксована висота кнопки */
  border: 2px solid white; /* біла рамка */
  background-color: transparent; /* прозорий фон */
  color: white; /* колір тексту */
  font-size: 16px; /* розмір шрифту */
  cursor: pointer; /* зміна курсору при наведенні */
  transition: background-color 0.3s, color 0.3s; /* плавна анімація зміни кольору */
}

/* Зміна стилю кнопок при наведенні миші */
button:hover {
  background-color: white; /* зміна фону при наведенні */
  color: black; /* зміна коліру тексту при наведенні */
}
</style>

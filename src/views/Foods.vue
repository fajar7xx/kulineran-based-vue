<template>
  <div>
    <Navbar />
    <div class="container">
      <div class="row mt-4">
        <div class="col">
          <h3>Daftar <strong>Makanan</strong></h3>
        </div>
      </div>

      <div class="row mt-3">
        <div class="col">
          <div class="input-group mb-3">
            
            <input
              type="text"
              class="form-control"
              placeholder="Cari Makanan Kesukaan Anda"
              aria-label="Cari"
              aria-describedby="basic-addon1"
							v-model="search"
							@keyup="searchFood()"
            />

						<div class="input-group-prepend">
              <span class="input-group-text" id="basic-addon1">
								<b-icon-search></b-icon-search>
							</span>
            </div>
          </div>
        </div>
      </div>

      <div class="row mb-4">
        <div
          class="col-md-4 mt-4"
          v-for="product in products"
          :key="product.id"
        >
          <CardProduct :product="product" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import CardProduct from "@/components/CardProduct.vue";
import axios from "axios";

export default {
  name: "Foods",
  components: {
    Navbar,
    CardProduct,
  },
  data() {
    return {
			products: [],
			search: [],
    };
  },
  methods: {
    setProduct(data) {
      this.products = data;
		},
		searchFood(){
			axios
      .get("http://localhost:3000/products?q=" + this.search)
      .then((response) =>
        // handle success
        // console.log("berhasil : ", response)
        this.setProduct(response.data)
      )
      .catch((error) =>
        // handle error
        console.log("gagal : ", error)
      );
		}
  },
  mounted() {
    axios
      .get("http://localhost:3000/products")
      .then((response) =>
        // handle success
        // console.log("berhasil : ", response)
        this.setProduct(response.data)
      )
      .catch((error) =>
        // handle error
        console.log("gagal : ", error)
      );
  },
};
</script>

<style>
</style>
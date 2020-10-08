<template>
  <div class="food-detail">
    <Navbar />
    <div class="container">
      <!-- breadcrumb -->
      <div class="row mt-5">
        <div class="col">
          <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
              <li class="breadcrumb-item">
                <router-link to="/" class="text-dark">Home</router-link>
              </li>
              <li class="breadcrumb-item">
                <router-link to="/foods" class="text-dark">Foods</router-link>
              </li>
              <li class="breadcrumb-item active" aria-current="page">
                Food Detail
              </li>
            </ol>
          </nav>
        </div>
      </div>

      <div class="row mt-4">
        <div class="col-md-6">
          <img
            :src="'../assets/images/' + product.gambar"
            class="img-fluid shadow"
          />
        </div>
        <div class="col-md-6">
          <h2>
            <strong>{{ product.nama }}</strong>
          </h2>
          <hr />
          <h4>
            Harga: <strong>Rp. {{ product.harga }}</strong>
          </h4>

          <form class="mt-4" @submit.prevent>
            <div class="form-group">
              <label for="jumlah_pemesanan">Jumlah Pesan</label>
              <input
                type="number"
                name="jumlah_pemesanan"
                id="jumlah_pemesanan"
                class="form-control"
                min="0"
                v-model="pesan.jumlah_pemesanan"
              />
            </div>
            <div class="form-group">
              <label for="keterangan">keterangan</label>
              <textarea
                name="keterangan"
                id="keterangan"
                rows="5"
                class="form-control"
                placeholder="Keterangan seperti: Pedas, Nasi setengan ......"
                v-model="pesan.keterangan"
              ></textarea>
            </div>

            <button type="submit" class="btn btn-success" @click="pemesanan()">
              <b-icon-cart></b-icon-cart>
              Pesan
            </button>
          </form>
        </div>
      </div>
    </div>
    <!-- <h1>Food Detail {{ $route.params.id }}</h1> -->
  </div>
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import axios from "axios";

// vue toast notification

export default {
  name: "FoodDetail",
  components: {
    Navbar,
  },
  data() {
    return {
      product: {},
      pesan: {},
    };
  },
  methods: {
    setProduct(data) {
      this.product = data;
    },
    pemesanan() {
      // console.log(this.pesan)
      if (this.pesan.jumlah_pemesanan) {

        // masukkan pesanan ke keranjang dengan
        // cara memasukkan nya ke views keranjang
        // menggunakan router push
        this.$router.push({path: "/keranjang"})

        
        this.pesan.products = this.product;
        axios
          .post("http://localhost:3000/keranjangs", this.pesan)
          .then(() => {
            // console.log("Berhasil")
            this.$toast.success("Sukses Masuk Keranjang.", {
              // optional options Object
              type: "success",
              position: "top-right",
              duration: 3000,
              dismissible: true,
            });
          })
          .catch((error) => console.log(error));
      } else {
        this.$toast.error("Jumlah Pesanan harus diisi.", {
          // optional options Object
          type: "error",
          position: "top-right",
          duration: 3000,
          dismissible: true,
        });
      }
    },
  },
  mounted() {
    axios
      .get("http://localhost:3000/products/" + this.$route.params.id)
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
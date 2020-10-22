<template>
  <div>
    <Navbar :updateKeranjang="keranjangs" />
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
                Keranjang
              </li>
            </ol>
          </nav>
        </div>
      </div>

      <div class="row">
        <div class="col">
          <h2>Keranjang <strong>Saya</strong></h2>

          <div class="table-responsive mt-3">
            <table class="table">
              <thead>
                <tr>
                  <th scope="col">#</th>
                  <th scope="col">Foto</th>
                  <th scope="col">Makanan</th>
                  <th scope="col">Keterangan</th>
                  <th scope="col">Jumlah</th>
                  <th scope="col">Harga</th>
                  <th scope="col">Total Harga</th>
                  <th scope="col">Hapus</th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="(keranjang, index) in keranjangs"
                  :key="keranjang.id"
                >
                  <td>{{ index + 1 }}</td>
                  <td>
                    <img
                      :src="'../assets/images/' + keranjang.products.gambar"
                      class="img-fluid shadow"
                      width="150"
                    />
                  </td>
                  <td>{{ keranjang.products.nama }}</td>
                  <td>{{ keranjang.keterangan || "-" }}</td>
                  <td>{{ keranjang.jumlah_pemesanan }}</td>
                  <td align="right">Rp. {{ keranjang.products.harga }}</td>
                  <td align="right">
                    Rp.
                    <strong>{{
                      keranjang.products.harga * keranjang.jumlah_pemesanan
                    }}</strong>
                  </td>
                  <td align="center" class="text-danger">
                    <b-icon-trash
                      @click="hapusKeranjang(keranjang.id)"
                    ></b-icon-trash>
                  </td>
                </tr>
                <tr>
                  <td colspan="6" align="right">
                    <strong> Total Harga: </strong>
                  </td>
                  <td align="right">
                    <strong> Rp. {{ totalHarga }} </strong>
                  </td>
                  <td></td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- form-checkout -->
      <div class="row justify-content-end">
        <div class="col-md-4">
          <form class="mt-4" @submit.prevent>
            <div class="form-group">
              <label for="nama">Nama Pemesan</label>
              <input
                type="text"
                name="nama"
                id="nama"
                class="form-control"
                min="0"
                v-model="pesan.nama"
              />
            </div>
            <div class="form-group">
              <label for="noMeja">No Meja</label>
              <input
                type="number"
                name="noMeja"
                id="noMeja"
                class="form-control"
                min="0"
                v-model="pesan.noMeja"
              />
            </div>

            <button
              type="submit"
              class="btn btn-success float-right"
              @click="checkout()"
            >
              <b-icon-cart></b-icon-cart>
              Pesan
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import axios from "axios";

export default {
  name: "Keranjang",
  components: {
    Navbar,
  },
  data() {
    return {
      keranjangs: [],
      pesan: {},
    };
  },
  methods: {
    setKeranjangs(data) {
      this.keranjangs = data;
    },
    hapusKeranjang(id) {
      axios
        .delete("http://localhost:3000/keranjangs/" + id)
        .then(() => {
          this.$toast.error("Sukses Hapus Item Keranjang.", {
            // optional options Object
            type: "error",
            position: "top-right",
            duration: 3000,
            dismissible: true,
          });

          // update/reload data
          axios
            .get("http://localhost:3000/keranjangs")
            .then((response) =>
              // console.log("Berhasil")
              this.setKeranjangs(response.data)
            )
            .catch((error) => console.log(error));
        })
        .catch((error) => console.log(error));
    },
    checkout() {
      // validasi
      if (this.pesan.nama && this.pesan.noMeja) {
        this.pesan.keranjangs = this.keranjangs;
        axios
          .post("http://localhost:3000/pesanans", this.pesan)
          .then(() => {
            // console.log("Berhasil")

            // hapus semua keranjang
            this.keranjangs.map(function (item) {
              axios
                .delete("http://localhost:3000/keranjangs/" + item.id)
                .catch((error) => console.log(error));
            });

            this.$router.push({ path: "/pesanan-sukses" });
            this.$toast.success("Sukses Di pesan.", {
              // optional options Object
              type: "success",
              position: "top-right",
              duration: 3000,
              dismissible: true,
            });
          })
          .catch((error) => console.log(error));
      } else {
        this.$toast.error("Nama dan No meja wajib di isi.", {
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
      .get("http://localhost:3000/keranjangs")
      .then((response) =>
        // console.log("Berhasil")
        this.setKeranjangs(response.data)
      )
      .catch((error) => console.log(error));
  },
  computed: {
    totalHarga() {
      return this.keranjangs.reduce(function (items, data) {
        return items + data.products.harga * data.jumlah_pemesanan;
      }, 0);
    },
  },
};
</script>

<style>
</style>
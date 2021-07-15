<template>
  <div class="product">
    <HeaderShayna />

    <!-- Breadcrumb Section Begin -->
    <div class="breacrumb-section text-left">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="breadcrumb-text product-more">
              <router-link to="/"><i class="fa fa-home"></i> Home</router-link>
              <span>Detail</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Breadcrumb Section Begin -->

    <!-- Product Shop Section Begin -->
    <section class="product-shop spad page-details">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="row">
              <div class="col-lg-6">
                <div class="product-pic-zoom">
                  <img class="product-big-img" :src="gambar_default" alt="" />
                </div>
                <div
                  class="product-thumbs"
                  v-if="productDetails.galleries.length > 0"
                >
                  <carousel
                    class="product-thumbs-track ps-slider"
                    :dots="false"
                    :nav="false"
                  >
                    <div
                      v-for="ScreenShot in productDetails.galleries"
                      :key="ScreenShot.id"
                      class="pt"
                      @click="changeImage(ScreenShot.photo)"
                      :class="
                        ScreenShot.photo == gambar_default ? 'active' : ''
                      "
                    >
                      <img :src="ScreenShot.photo" alt="" />
                    </div>
                  </carousel>
                </div>
              </div>
              <div class="col-lg-6">
                <div class="product-details text-left">
                  <div class="pd-title">
                    <span>{{ productDetails.type }}</span>
                    <h3>{{ productDetails.name }}</h3>
                  </div>
                  <div class="pd-desc">
                    <p v-html="productDetails.description"></p>
                    <h4>${{ productDetails.price }}</h4>
                  </div>
                  <div class="quantity">
                    <router-link
                      to="/cart"
                      class="primary-btn pd-cart"
                      @click="
                        saveKeranjang(
                          productDetails.id,
                          productDetails.name,
                          productDetails.price,
                          productDetails.galleries[0].photo
                        )
                      "
                      >Add To Cart</router-link
                    >
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- Product Shop Section End -->

    <RelatedShayna />

    <FooterShayna />
  </div>
</template>

<script>
import HeaderShayna from "@/components/HeaderShayna.vue";
import FooterShayna from "@/components/FooterShayna.vue";
import carousel from "vue-owl-carousel";
import RelatedShayna from "@/components/RelatedShayna.vue";
import axios from "axios";

export default {
  name: "Product",
  components: {
    HeaderShayna,
    FooterShayna,
    carousel,
    RelatedShayna,
  },
  data() {
    return {
      gambar_default: "",
      productDetails: [],
      keranjangUser: [],
    };
  },
  methods: {
    changeImage(urlImage) {
      this.gambar_default = urlImage;
    },
    setDataPicture(data) {
      //Replace object productDetails dengan data dari API
      this.productDetails = data;
      //Replace value gambar default dengan data dari API (galleries)
      this.gambar_default = data.galleries[0].photo;
    },
    saveKeranjang(idProduct, nameProduct, priceProduct, photoProduct) {
      var productStored = {
        id: idProduct,
        name: nameProduct,
        price: priceProduct,
        photo: photoProduct,
      };

      this.keranjangUser.push(productStored);
      const parsed = JSON.stringify(this.keranjangUser);
      localStorage.setItem("keranjangUser", parsed);
    },
  },
  mounted() {
    if (localStorage.getItem("keranjangUser")) {
      try {
        this.keranjangUser = JSON.parse(localStorage.getItem("keranjangUser"));
      } catch (e) {
        localStorage.removeItem("keranjangUser");
      }
    }
    axios
      .get("http://shayna-backend.test/api/products", {
        params: {
          id: this.$route.params.id,
        },
      })
      .then((res) => this.setDataPicture(res.data.data))
      // eslint-disable-next-line no-console
      .catch((err) => console.log(err));
  },
};
</script>

<style scoped>
.product-thumbs .pt {
  margin-right: 14px;
}
</style>

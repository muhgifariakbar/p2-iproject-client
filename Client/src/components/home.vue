<template>
  <div class="container">
    <div
      class="container shadow-sm p-3 mb-5 rounded width-5 mt-5 mb-1 opacity-25"
      style="background: rgba(0, 0, 0, 0.5)"
    >
      <h1 class="text-center text-white display-1 font-weight-bold">
        ANIMEDORO
      </h1>
    </div>
    <!-- SEARCH BAR -->
    <section class="d-flex justify-content-center ml-5">
      <div class="d-flex justify-content-center ml-5">
        <div class="input-group">
          <form @submit.prevent="anime" class="w-40 d-flex">
            <input
              v-on:change="anime"
              type="search"
              class="form-control rounded d-flex justify-content-center"
              style="width: 300px"
              placeholder="Search"
              v-model="search"
            />
            <button
              type="submit"
              class="btn btn-dark d-flex justify-content-center"
            >
              search
            </button>
          </form>
        </div>
      </div>
    </section>
    <!-- END OF SEARCH BAR -->
    <!-- card -->
    <h5 class="text-center text-light"></h5>

    <div v-if="isLoading">
      <img
        src="https://raw.githubusercontent.com/gist/s-shivangi/7b54ec766cf446cafeb83882b590174d/raw/8957088c2e31dba6d72ce86c615cb3c7bb7f0b0c/nyan-cat.gif"
      />
      <h1>ENJOY THE CAT WHILE LOADING...</h1>
    </div>
    <div class="row ml-5 mt-5" v-if="!isLoading">
      <div
        v-for="(e, i) in dataAnime"
        :key="i"
        class="card text-white bg-dark mt-5 mx-auto"
        style="width: 18rem"
      >
        <img
          class="card-img-top mt-4 rounded mx-auto d-block"
          :src="e.images.jpg.large_image_url"
          alt="Card image cap"
        />
        <div class="card-body opacity-25">
          <h5 class="card-title text-center">{{ e.title }}</h5>
          <p class="card-text text-center">{{ e.title_japanese }}</p>
          <p class="card-text text-center">Released Year: {{ e.year }}</p>
          <button
            @click="openTrailer(e.title, e.trailer.url)"
            class="btn btn-primary mt-auto text-center"
          >
            Watch Movie
          </button>
        </div>
        <!-- start sharing social media -->
        <span class="d-flex justify-content-center"> Share on :</span>
        <div class="container d-flex justify-content-center">
          <ShareNetwork
            network="facebook"
            url="https://youtu.be/{{e.trailer.youtube_id}}"
            title="Say Hi to ANIMEDORO."
            description="Ayo nonton {{e.title}} di ANIMEDORO sekaligus Judi!"
            quote="super ANIMEDORO"
            hashtags="jud,online,anime"
            class="fa fa-facebook mt-1"
          >
          </ShareNetwork>
          <ShareNetwork
            network="twitter"
            url="https://youtu.be/{{e.trailer.youtube_id}}"
            title="Say Hi to ANIMEDORO."
            quote="super ANIMEDORO"
            hashtags="jud,online,anime"
            class="fa fa-twitter"
          >
          </ShareNetwork>
        </div>
      </div>
    </div>

    <nav
      aria-label="Page navigation example"
      class="d-flex justify-content-center mt-3"
    >
      <ul class="pagination">
        <li class="page-item">
          <a
            class="page-link"
            href="#"
            @click.prevent="changePage(-1)"
            :hidden="page <= 1 ? true : false"
            >Previous</a
          >
        </li>
        <li class="page-item">
          <a class="page-link" href="#">{{ page }}</a>
        </li>
        <li class="page-item">
          <a class="page-link" href="#" @click.prevent="changePage(1)">Next</a>
        </li>
      </ul>
    </nav>
  </div>
</template>

<script>
import { mapActions, mapState } from "pinia";
import { useCounterStore } from "../stores/counter";
import Swal from "sweetalert2";
import "vue3-carousel/dist/carousel.css";
import VueSocialSharing from "vue-social-sharing";

export default {
  data() {
    return {
      page: 1,
      isPremium: false,
      showAdds: true,
      gambarJudi: [
        "https://media.suara.com/pictures/653x366/2022/05/15/59873-ilustrasi-judi-online.jpg",
        "https://assets.promediateknologi.com/crop/0x0:0x0/x/photo/2022/07/10/2372856385.jpg",
        "https://cmupress.cmu.ac.th/slot-deposit-pulsa/situs-slot-gacor.png",
        "https://bukutekno.com/wp-content/uploads/2019/11/1.Tunaiku-630x380.png",
        "https://i02.appmifile.com/82_bbs_en/05/09/2022/760bfe5c46.jpg",
      ],
      search: "",
    };
  },
  computed: {
    ...mapState(useCounterStore, ["dataAnime", "isLoading"]),
  },
  methods: {
    ...mapActions(useCounterStore, [
      "getAnime",
      "checkLogin",
      "showAddsTrue",
      "isLoadingTrue",
      "isLoadingFalse",
    ]),
    async changePage(page) {
      this.page = this.page + page;
      console.log(this.page, "asdfkj");
      await this.getAnime(this.page, this.search);
    },
    anime() {
      // console.log("hi");
      this.getAnime(this.page, this.search);
    },
    checkPremium() {
      if (
        (localStorage.getItem("paid") === "false" &&
          this.showAdds &&
          this.$route.fullPath === "/") ||
        (!localStorage.getItem("paid") &&
          this.showAdds &&
          this.$route.fullPath === "/")
      ) {
        let image = this.getRandomItem();
        console.log(image, "image");
        setTimeout(() => {
          Swal.fire({
            html: `<img src="${image}" width="600" height='500'>`,
            width: 750,
            background: "#2A363B",
            footer: `<a class="btn btn-primary" href="https://mesin-mpo.com/register/76B1F7A9">Mulai Menghasilkan Uang Secara Instant!</a>`,
          });
          this.checkPremium();
        }, 10000);
      }
    },
    openTrailer(anime, videoUrl) {
      videoUrl = `https://www.youtube.com/embed/${videoUrl.split("=")[1]}`;
      console.log(videoUrl, "<<");
      Swal.fire({
        title: `<strong>${anime} Trailer</strong>`,
        html: `<iframe width="1500" height="500" src="${videoUrl}" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>`,
        showCloseButton: true,
        // showCancelButton: true,
        showConfirmButton: false,
        // focusConfirm: false,
        footer: `<a class="btn btn-primary" href="https://mesin-mpo.com/register/76B1F7A9">Judi Mulai Rp 100K jadi Rp 1Jt</a>`,
        width: 1500,
      });
    },
    getRandomItem() {
      // get random index value
      const randomIndex = Math.floor(Math.random() * this.gambarJudi.length);

      // get random item
      const item = this.gambarJudi[randomIndex];
      console.log(item, "item");
      return item;
    },
  },
  created() {
    this.isLoadingTrue();
    this.getAnime(this.page);
    this.isLoadingFalse();
    // this.showAdds = true;
    // this.showAddsTrue();
    this.checkPremium();
    this.getRandomItem();
  },
};
</script>

<style>
.card-img-top {
  width: 100%;
  height: 15vw;
  object-fit: cover;
}
.card-body {
  display: flex;
  flex-direction: column;
}
</style>

<template>
  <div>
    <h2 class="banners-title">
      На главной Новости Акции внизу
      <Switcher class="banners-switcher" @stateChanged="showToggle"/>
    </h2>
    <div class="news-wrap" v-show="showBottomSliderConfig">
      <div class="top-slider__text top-text">Размер: 1000х190</div>

      <div class="top-slider__top">
        <div class="top-slider__slides">
          <BottomSlideEdit
              v-for="(slide, index) in slides"
              :key="slide.id"
              :index="index"
              :slideUrl="slide.url"
              :slideImage="slide.image"
              @removeSlide="removeSlide"
              @editUrl="editUrl"
              @changeImage="changeImage"
          />
        </div>

        <div class="top-slider__add-button">
          <a @click="addSlide" class="btn btn-app">
            <i class="fas fa-play"></i> Добавить фото
          </a>
        </div>
      </div>

      <div class="top-slider__bottom">
        <div class="top-slider__speed">
          <div class="form-group">
            <label>Скорость вращения</label>
            <select
                class="form-control select2bs4 select2-hidden-accessible"
                style="width: 100%"
                data-select2-id="17"
                tabindex="-1"
                aria-hidden="true"
                v-model="sliderSpeed"
            >
              <option selected data-select2-id="19">2</option>
              <option data-select2-id="81">4</option>
              <option data-select2-id="82">6</option>
            </select>
          </div>
        </div>

        <button
            @click="saveSliderSettings"
            class="btn bg-gradient-success save-btn"
            :disabled="isFetching"
        >
          Сохранить
        </button>
      </div>
    </div>

  </div>
</template>

<script>
import Switcher from "../../general/Switcher.vue";
import BottomSlideEdit from "./BottomSlideEdit.vue";
import firebase from "firebase";
import db from "../../../../firebase/firebaseInit";
import {mapGetters} from 'vuex'

export default {
  name: "BottomNews",
  components: {Switcher, BottomSlideEdit},
  data() {
    return {
      slides: [],
      slideImageFiles: [],
      sliderSpeed: 2,
      showBottomSliderConfig: true,
      isFetching: null,
      isInit: false,
    };
  },
  methods: {
    addSlide() {
      this.slides.push({
        id: +Math.random(),
        image: ".../../../assets/image/empty.jpg",
        url: "",
        imageChanged: null,
      });
    },
    showToggle(status) {
      this.showBottomSliderConfig = status;
    },
    changeImage(index, file) {
      let reader = new FileReader();
      this.slideImageFiles[index] = file;

      reader.onload = () => {
        this.slides[index].image = reader.result;
        this.slides[index].imageChanged = true;
      };

      reader.readAsDataURL(file);
    },
    removeSlide(index) {
      this.slides.splice(index, 1);
    },
    editUrl(index, url) {
      this.slides[index].url = url;
    },

    async setImageRef(slide) {
      const storageRef = firebase.storage().ref();

      let ref = storageRef.child(
          `Banners&Sliders/BottomNewsSlider/${this.currentLang}/slide-${slide}.jpg`
      );

      await ref.put(this.slideImageFiles[slide])
      let link = await ref.getDownloadURL()
      this.slides[slide].image = link
    },

    async saveSliderSettings() {
      this.isFetching = true;

      await Promise.all(this.slides.map(async (slide, index) => {
        if (slide.imageChanged) {
          await this.setImageRef(index);
          this.slides[index].imageChanged = false;
        }
      }))

      const bottomNewsSliderConfig = await db
          .collection("Banners&Sliders")
          .doc("bottomNewsSlider")
          .collection('lang').doc(this.currentLang)

      await bottomNewsSliderConfig.set({
        slidesConfig: this.slides,
        speed: this.sliderSpeed,
      });

      this.isFetching = false;
    },

    async getBottomNewsSliderConfig() {
      const slides = await db
          .collection("Banners&Sliders")
          .doc("bottomNewsSlider")
          .collection('lang').doc(this.currentLang).get();
      const data = slides.data();

      if (data) {
        this.slides = data.slidesConfig;
        this.sliderSpeed = data.speed;
      } else {
        this.slides = [];
        this.sliderSpeed = 2;
      }

      this.isInit = true;
    },
  },
  computed: mapGetters(['currentLang']),
  watch: {
    currentLang() {
      this.getBottomNewsSliderConfig()
    }
  },
  created() {
    this.getBottomNewsSliderConfig()
  }
};
</script>

<style lang="scss" scoped>
</style>
<template>
  <Preloader v-if="!cinema.isInit"/>
  <div v-else class="cinema-edit">
    <InputWithText :text="cinemaLang.title" v-model="cinema.title"/>

    <TextAreaWithText :text="cinemaLang.description" v-model="cinema.description"/>

    <TextAreaWithText :text="cinemaLang.conditions" v-model="cinema.conditions"/>

    <ImageWithTwoButton
        :text="cinemaLang.logotype"
        @removeImage="removeLogotypeImage"
        @changeImage="changeLogotypeImage"
        :image="cinema.logotypeImage"
        :addText="cinemaLang.addText"
        :removeText="cinemaLang.removeText"
    />

    <ImageWithTwoButton
        :text="cinemaLang.topBanner"
        @removeImage="removeTopBannerImage"
        @changeImage="changeTopBannerImage"
        :image="cinema.topBannerImage"
        :addText="cinemaLang.addText"
        :removeText="cinemaLang.removeText"
    />

    <ImagesRow
        @change="changeImages"
        :data="cinema.images"
        :text="cinemaLang.imageRowSizeText"
        :btnText="cinemaLang.imageBtnRowText"
    />

    <HallList
        :halls="halls"
        @removeHall="removeHall"
    />

    <Seo
        :text="cinemaLang.seo"
        :value="cinema.seo"
        @url="editSeoUrl"
        @title="editSeoTitle"
        @keywords="editSeoKeywords"
        @description="editSeoDescription"
    />

    <SaveButton
        :text="cinemaLang.saveButtonText"
        @saveEvent="saveCinema"
        :isFetching="cinema.isFetching"
    />


  </div>
</template>

<script>
import TextAreaWithText from "../../../components/admin/general/TextAreaWithText";
import ImageWithTwoButton from "../../../components/admin/general/ImageWithTwoButton";
import InputWithText from "../../../components/admin/general/InputWithText";
import Seo from "../../../components/admin/general/Seo";
import ImagesRow from "../../../components/admin/general/imagesRow/ImagesRow";
import SaveButton from "../../../components/admin/general/SaveButton";
import HallList from "../../../components/admin/cinemas/halls/HallList";
import Preloader from "../../../components/admin/general/Preloader";

export default {
  name: "CinemaEditPage.vue",
  components: {
    Preloader,
    HallList, SaveButton, Seo, InputWithText, ImageWithTwoButton, TextAreaWithText, ImagesRow
  },
  props: {
    cinema: {
      type: Object
    },
    halls: {
      type: Array
    },
    cinemaLang: {
      type: Object
    }
  },
  methods: {
    removeLogotypeImage() {
      this.$emit('removeLogotypeImage')
    },
    editSeoUrl(url) {
      this.$emit('url', url)
    },
    editSeoTitle(title) {
      this.$emit('title', title)
    },
    editSeoKeywords(keywords) {
      this.$emit('keywords', keywords)
    },
    editSeoDescription(description) {
      this.$emit('description', description)
    },
    changeLogotypeImage(file) {
      this.$emit('changeLogotypeImage', file)
    },

    removeTopBannerImage() {
      this.$emit('removeTopBannerImage')
    },
    changeTopBannerImage(file) {
      this.$emit('changeTopBannerImage', file)
    },

    changeImages(file, index) {
      this.$emit('changeImages', file, index)
    },
    uploadImages() {
      this.$emit('uploadImages')
    },
    changeSeo(data) {
      this.$emit('changeCinemaSeo', data)
    },
    changeLang(lang) {
      this.$emit('changeCinemaLang', lang)
    },
    saveCinema() {
      this.$emit('saveCinema')
    },
    removeHall(index) {
      this.$emit('removeHall', index)
    }
  }
}
</script>

<style scoped>
</style>
<template lang="pug">
  v-container
    v-row
      v-col(cols='12' lg='6')
        v-form(
          v-model='isValid'
          ref='addForm'
          lazy-validation
        )
          v-text-field(
            v-model='name'
            :rules='nameRules'
            label='Name'
            required
            dark
            autofocus
            @keyup.enter='saveWaifu'
          )
          v-text-field(
            v-model='imgUrl'
            :rules='imageRules'
            label='Image URL'
            required
            dark
            @keyup.enter='saveWaifu'
          )
          v-textarea.add__textarea(
            v-model='description'
            :rules='descriptionRules'
            counter='950'
            label='Description'
            auto-grow
            dark
            dense
            rows='2'
            row-height='20'
          )

          v-btn(
            dark
            :disabled='!isValid || loading'
            :loading='loading'
            @click='saveWaifu'
          ) Add waifu

      v-col(cols='12' lg='6')
        v-sheet.px-3.pt-3.pb-3.add__sheet(dark v-if='imgUrl.length < 1')
          v-skeleton-loader.mx-auto(type='image')
          h3.add__image-preview Image preview, please enter the url

        v-card.add__image-card(v-else dark min-height="200px")
          v-img(
            dark
            :src='imgUrl'
            :lazy-src='imgUrl'
            class='add__img'
          )
            template(v-slot:placeholder)
              v-row.fill-height.add__progress
                v-progress-circular(indeterminate color='#fff')
</template>

<script>
export default {

  data: () => ({
    isValid: false,

    name: '',
    imgUrl: '',
    description: '',

    nameRules: [
      v => !!v || 'Name is required'
    ],
    imageRules: [
      v => !!v || 'Image url is required'
    ],
    descriptionRules: [
      v => v.length < 950 || 'Text length is too long'
    ]
  }),

  computed: {
    loading () {
      return this.$store.getters['loadingStore/getLoading']
    },

    user () {
      return this.$store.getters['userStore/getUser']
    }
  },

  methods: {
    saveWaifu () {
      if (this.$refs.addForm.validate()) {
        const { name, imgUrl, description, user } = this

        this.$store.dispatch('waifuStore/saveWaifu', {
          name, imgUrl, description, user
        })
      }
    }
  },
  head () {
    return {
      title: 'Add'
    }
  },

  middleware: ['auth.middle']
}
</script>

<style lang="sass" scoped>
.add
  &__textarea
    margin-top: 20px

  &__sheet
    position: relative

  &__image-preview
    position: absolute
    top: calc(50% - 11px)
    right: 0
    left: 0
    width: max-content
    margin: 0 auto
    padding: 0 10px

  &__image-card
    display: flex

  &__progress
    align-items: center
    justify-content: center
</style>

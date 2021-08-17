<template>
  <q-page class="flex flex-center">
    <template v-if="!visible">
      <img
        alt="Quasar logo"
        src="~assets/quasar-logo-vertical.svg"
        style="width: 100px; height: 100px"
      >
    </template>
    <template v-else>
      <div class="q-pa-lg q-gutter-xl">
        <q-card class="my-card bg-dark text-white">
          <q-img :src="background ?? 'https://via.placeholder.com/150'">
            <div class="absolute-full flex flex-center">
              <div class="text-h6 text-center text-capitalize">{{ description ?? 'description' }}</div>
            </div>
          </q-img>
        </q-card>
        <q-card class="my-card bg-accent text-white">
          <q-card-section>
            <div class="text-h6">Our Changing Planet</div>
            <div class="text-subtitle2">by John Doe</div>
          </q-card-section>

          <q-card-section>
            Loremipsum
          </q-card-section>

          <q-separator dark/>

          <q-card-actions>
            <q-btn flat>Action 1</q-btn>
            <q-btn flat>Action 2</q-btn>
          </q-card-actions>
        </q-card>
      </div>
      <div class="q-pa-lg q-gutter-xl">
        <q-card class="my-card bg-dark text-white">
          <q-card-section>
            <div class="text-h6">Our Changing Planet</div>
            <div class="text-subtitle2">by John Doe</div>
          </q-card-section>

          <q-card-section>
            Loremipsum
          </q-card-section>

          <q-separator dark/>

          <q-card-actions>
            <q-btn flat>Action 1</q-btn>
            <q-btn flat>Action 2</q-btn>
          </q-card-actions>
        </q-card>
        <q-card class="my-card bg-accent text-white">
          <q-card-section>
            <div class="text-h6">Our Changing Planet</div>
            <div class="text-subtitle2">by John Doe</div>
          </q-card-section>

          <q-card-section>
            Loremipsum
          </q-card-section>

          <q-separator dark/>

          <q-card-actions>
            <q-btn flat>Action 1</q-btn>
            <q-btn flat>Action 2</q-btn>
          </q-card-actions>
        </q-card>
      </div>
    </template>
  </q-page>
</template>

<script>

import { inject, ref, watch } from 'vue'

const Flickr = require('@enricu/flickr-sdk')

export default {
  setup () {
    console.log(process.env.FLIKR_API_KEY)
    const flickr = new Flickr(process.env.FLIKR_API_KEY)
    const data = inject('weatherData')
    const icon = ref(null)
    const background = ref(null)
    const description = ref(null)
    const name = ref(null)
    const visible = ref(false)
    // const USPLASH_API_KEY = process.env.USPLASH_API_KEY
    watch(data, async () => {
      description.value = data.value?.weather[0].description
      name.value = data.value?.name
      icon.value = `https://openweathermap.org/img/wn/${data.value?.weather[0].icon}@4x.png`
      await getbackground()
      visible.value = name.value !== null
    })

    const resolverbackground = query => {
      return new Promise(resolve => {
        flickr.photos.search({
          text: query,
          sort: 'interestingness-desc',
          accuracy: 11,
          content_type: 1,
          media: 'photos',
          per_page: 10,
          privacy_filter: 1
        }).then(res => resolve(res.body.photos))
          .catch(err => console.error(err))
      })
    }
    const getbackground = async () => {
      let raw = await resolverbackground(name.value)
      console.log({ raw })
      const random = Math.floor(Math.random() * 10)
      raw = raw.photo[random]
      background.value = `https://live.staticflickr.com/${raw.server}/${raw.id}_${raw.secret}_n.jpg`
    }

    return {
      visible,
      data,
      icon,
      background,
      description,
      name
    }
  }
}
</script>
<style lang="sass" scoped>
.my-card
  width: 100%
  max-width: 50vw
</style>

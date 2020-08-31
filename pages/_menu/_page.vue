<template>
<v-main :style="style">
  <Section :section="section" :menu="menu" />
</v-main>
</template>

<script>
export default {
  async asyncData ({ app, $config, route }) {
    const menus = await app.$axios.$get(`https://${$config.SERVICE_ID}.microcms.io/api/v1/menu`, {
      headers: { 'X-API-KEY': $config.API_KEY }
    })
    const section = await app.$axios.$get(`https://${$config.SERVICE_ID}.microcms.io/api/v1/section/${route.params.page}`, {
      headers: { 'X-API-KEY': $config.API_KEY }
    })
    const menu = menus.contents.find(x => x.id === route.params.menu)
    return { menus, section, menu }
  },
  mounted () {
    this.setMenus()
  },
  computed: {
    style () {
      if (this.$vuetify.breakpoint.mdAndUp) {
        return `margin-top:64px;`
      }
      else {
        return `margin-top:56px;`
      }
    }
  },
  methods: {
    setMenus () {
      this.$nuxt.$emit('setMenus', {menus: this.menus})
    }
  },
  head () {
    return {
      title: this.section.title,
      meta: [
        { hid: 'og:title', property: 'og:title', content: this.section.title },
      ]
    }
  }
}
</script>
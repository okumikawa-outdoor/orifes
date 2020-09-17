<template>
<v-main :style="style">
  <List :menu="menu" :sections="sections.contents" />
</v-main>
</template>

<script>

export default {
  async asyncData ({ app, $config, route }) {
    const menus = await app.$axios.$get(`https://${$config.SERVICE_ID}.microcms.io/api/v1/menu`, {
      headers: { 'X-API-KEY': $config.API_KEY }
    })
    const sections = await app.$axios.$get(`https://${$config.SERVICE_ID}.microcms.io/api/v1/section?limit=100&filters=menu[equals]${route.params.menu}`, {
      headers: { 'X-API-KEY': $config.API_KEY }
    })
    const menu = menus.contents.find(x => x.id === route.params.menu)
    return { menus, sections, menu }
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
      title: this.menu.title,
      meta: [
        { hid: 'og:title', property: 'og:title', content: this.menu.title },
      ]
    }
  }
}
</script>
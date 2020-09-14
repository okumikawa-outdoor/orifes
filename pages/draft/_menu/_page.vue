<template>
<v-main :style="style">
  <template v-if="section">
    <Section :section="section" :menu="menu" />
  </template>
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
    return { menus, menu }
  },
  async created() {
    const id = this.$route.params.page
    const query = this.$route.query
    if (id === undefined || query.draftKey === undefined) {
      return
    }
    const section = await this.$axios.$get(`https://${this.$config.DRAFT_SERVICE_ID}.microcms.io/api/v1/section/${id}?draftKey=${query.draftKey}`, {
      headers: { 'X-API-KEY': this.$config.DRAFT_API_KEY }
    })
    this.section = section;
  },
  data () {
    return {
      section: null
    }
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
  }
}
</script>
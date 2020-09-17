<template>
<div
  :style="(bgImg)? `background:no-repeat center/cover url('${bgImg}');` : `background-color: ${color.bg};`"
>
  <template v-if="menu.bread && $route.params.menu">
  <Bread :menu="menu" />
  </template>
  
  <template v-if="menu.content">
  <v-container
      class="pt-4 pt-sm-6"
  >
    <v-row no-gutters>
      <v-col>
        <Content :item="menu" :class="$style.border" />
      </v-col>
    </v-row>
  </v-container>
  </template>
  <template v-if="listType == null">
    <template v-for="section in filterdSections">
    <Section :section="section" />
    </template>
  </template>
  <template v-else-if="listType === 'ライン'">
    <v-container
      class="pb-4 pb-sm-6"
    >
      <v-list
        :color="color.bg"
        class="py-0"
      >
        <template v-for="section in filterdSections">
        <v-divider></v-divider>
        <LineList :section="section" :menu="menu" />
        </template>
        <v-divider></v-divider>
        <template v-if="$route.path === '/'">
          <v-row no-gutters>
            <v-spacer></v-spacer>
            <v-col cols="auto">
              <v-btn
                :small="$vuetify.breakpoint.xs"
                :to="`/${menu.id}`"
                nuxt
                text
              >
                一覧を見る
              </v-btn>
            </v-col>
          </v-row>
        </template>
      </v-list>
    </v-container>
  </template>
  <template v-else-if="listType === 'カード'">
    <v-container
      class="pb-4 pb-sm-6"
    >
      <v-row>
        <template v-for="section in filterdSections">
        <v-col
          cols="12"
          sm="6"
          md="4"
          class="align-self-stretch"
        >
          <CardList :section="section" :menu="menu" />
        </v-col>
        </template>
      </v-row>
      <template v-if="$route.path === '/'">
        <v-row no-gutters>
          <v-spacer></v-spacer>
          <v-col cols="auto">
            <v-btn
              :small="$vuetify.breakpoint.xs"
              :to="`/${menu.id}`"
              nuxt
              text
            >
              一覧を見る
            </v-btn>
          </v-col>
        </v-row>
      </template>
    </v-container>
  </template>
</div>
</template>

<script>
export default {
  props: ['sections', 'menu'],
  data () {
    return {
      color: {
        bg: '#fff'
      }
    }
  },
  mounted () {
    this.color = {
      bg: this.$config.COLOR_LIST_BG
    }
  },
  computed: {
    listType () {
      if (this.menu.list != null) {
        if (this.menu.list.type) {
          return this.menu.list.type[0]
        }
      }
    },
    bgImg () {
      if (this.menu.list != null) {
        if (this.menu.list.bgImg) {
          const val = {
            xs: 600,
            sm: 960,
            md: 1264,
            lg: 1264,
            xl: 1264
          }
          const size = this.bp(val)
          var color = this.color.bg
          if (color.includes('#')) {
            color = color.slice(1)
          }
          else if (color.includes('rgba')) {
            color = color.slice(5)
            color = color.slice(0, -1)
            const rgba = color.split(',')
            const a = Math.round(rgba[3] * 255)
            color = this.change(a) + this.change(Number(rgba[0])) + this.change(Number(rgba[1])) + this.change(Number(rgba[2]))
          }
          else if (color.includes('gba')) {
            color = color.slice(4)
            color = color.slice(0, -1)
            const rgba = color.split(',')
            color = this.change(Number(rgba[0])) + this.change(Number(rgba[1])) + this.change(Number(rgba[2]))
          }
          return this.menu.list.bgImg.url + `?auto=compress&fit=clip&w=${size}&blend-mode=normal&blend-color=${color}` 
        }
      }
    },
    filterdSections () {
      if (this.sections) {
        if (this.$route.path === '/') {
          if (this.menu.list != null) {
            if (this.menu.list.num) {
              return this.sections.filter((x,i) => i < this.menu.list.num)
            }
          }
        }
        return this.sections
      }
    }
  },
  methods: {
    bp (x) {
      if (this.$vuetify.xs) {
        return x.xs
      }
      else if (this.$vuetify.sm) {
        return x.sm
      }
      else if (this.$vuetify.md) {
        return x.md
      }
      else if (this.$vuetify.lg) {
        return x.lg
      }
      else if (this.$vuetify.xl) {
        return x.xl
      }
    },
    change (z) {
      if (z < 16) {
        return 0 + z.toString(16)
      }
      else {
        return z.toString(16)
      }
    }
  }
}
</script>

<style module>
.border h1 {
  line-height:1;
}
.border u {
  text-decoration:none;
  border-bottom: dotted 5px #110033;
  padding:1.5rem;
}
</style>
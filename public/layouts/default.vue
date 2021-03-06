<template>
  <v-app v-if="config" style="background-color: #fcfcfc;">
    <dynamic-style />
    <client-only><accept-cookies /></client-only>
    <v-container class="pb-0">
      <v-row align="center">
        <v-col
          md="4"
          sm="6"
          cols="12"
          :class="{'pt-1 pb-0': true, 'text-center': $vuetify.breakpoint.xs}"
        >
          <a
            v-if="config.website"
            :href="config.website || '/'"
            style="height:100%"
            class="mr-3"
          >
            <v-img
              :src="logoUrl"
              :alt="config.title"
              contain
              style="height:80px"
            />
          </a>
          <nuxt-link
            v-else
            to="/"
            style="height:100%"
            class="mr-3"
          >
            <v-img
              :src="logoUrl"
              :alt="config.title"
              contain
              style="height:80px"
            />
          </nuxt-link>
        </v-col>
        <v-col
          md="8"
          sm="6"
          cols="12"
          :class="`pt-0 mb-3 ${$vuetify.breakpoint.xs ? 'text-center' : ''}`"
        >
          <h1 :class="`${$vuetify.breakpoint.xs ? 'headline' : 'display-1'} grey--text text--darken-2 font-weight-bold`">
            {{ config.title }}
          </h1>
        </v-col>
      </v-row>
    </v-container>

    <app-bar class="mb-3" />

    <v-main>
      <nuxt-child />
    </v-main>

    <v-footer :class="'pa-0 mt-5 ' + (footerColorDark ? 'area--dark' : 'area--light')" height="auto">
      <v-card
        :color="config.footerColor"
        tile
        width="100%"
      >
        <v-container>
          <v-row class="py-3">
            <v-col
              v-for="link in config.footerLinks"
              :key="link.title"
              cols="9"
              sm="4"
              offset="3"
              offset-sm="2"
              class="pa-0"
            >
              <template v-if="link.type === 'internal'">
                <nuxt-link v-if="link.page" :to="{name: 'pages-id', params: {id: link.page.id}}">
                  {{ link.page.title }}
                </nuxt-link>
              </template>
              <a v-else :href="link.href">{{ link.title }}</a>
            </v-col>
          </v-row>
        </v-container>

        <v-divider :color="footerColorDark ? 'white' : textDark" />

        <v-container class="text-center py-1">
          <span>&copy;{{ new Date().getFullYear() }} — </span><strong><a href="https://koumoul.com">Koumoul</a></strong>
          <!-- Réalisé avec <v-icon color="red" small>mdi-heart</v-icon> par <strong><nuxt-link :to="localePath({name: 'index'})">Koumoul</nuxt-link></strong> -->
        </v-container>
      </v-card>
    </v-footer>
  </v-app>
</template>

<script>
  import AcceptCookies from '~/components/accept-cookies'
  import AppBar from '~/components/layout/app-bar'
  import DynamicStyle from '~/components/layout/dynamic-style'
  const { mapState, mapGetters } = require('vuex')

  export default {
    components: { AcceptCookies, AppBar, DynamicStyle },
    computed: {
      ...mapState(['config', 'textDark', 'portal', 'draft']),
      ...mapGetters(['themeColorDark', 'footerColorDark']),
      logoUrl() {
        return `${process.env.publicUrl}/api/v1/portals/${this.portal._id}/assets/logo?draft=${this.draft}`
      },
    },
    head() {
      // For i18n support, see https://github.com/nuxt/nuxtjs.org/blob/master/layouts/default.vue
      const canonical = process.env.publicUrl + this.$route.path
      const link = [
        { rel: 'canonical', href: canonical },
      ]
      link.push({ rel: 'icon', type: 'image/x-icon', href: `${process.env.publicUrl}/api/v1/portals/${this.portal._id}/assets/favicon?draft=${this.draft}` })
      link.forEach((l) => {
        if (l.href.slice(-1) === '/') {
          l.href = l.href.slice(0, -1)
        }
      })
      const meta = [
        { name: 'twitter:card', content: 'summary' },
        { hid: 'og:title', property: 'og:title', content: this.config.title },
        { property: 'og:locale', content: 'fr_FR' },
        { hid: 'og:image', property: 'og:image', content: `${process.env.publicUrl}/api/v1/portals/${this.portal._id}/assets/home?draft=${this.draft}` },
        { hid: 'og:image:width', property: 'og:image:width', content: 567 },
        { hid: 'og:image:height', property: 'og:image:height', content: 383 },
      ]
      if (this.config.twitter) meta.push({ name: 'twitter:site', content: this.config.twitter })
      return {
        meta,
        link,
      }
    },
  }

</script>

<style>
@media only screen and (min-width: 1904px){
  .container.padded {
    max-width:100%!important;
  }

  .container {
    max-width:1315px!important;
  }
}

iframe {
  background-color: transparent;
  border: none;
}

.v-tabs__slider {
    height: 4px!important;
}

.v-card__text, .v-card__title {
  word-break: normal; /* maybe !important  */
}
</style>

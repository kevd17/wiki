<template lang='pug'>
  v-app
    .newpage
      .newpage-content
        img.animated.fadeIn(src='/svg/icon-delete-file.svg', alt='Not Found')
        .headline {{ $t('newpage.title') }}
        .subtitle-1.mt-3 {{ $t('newpage.subtitle') }}
        v-btn.mt-5(:href='`/e/` + locale + `/` + path', x-large)
          v-icon(left) mdi-plus
          span {{ $t('newpage.create') }}
        v-btn.mt-5(@click='redirect', depressed, x-large)
          v-icon(left) mdi-link-variant
          span {{ $t('newpage.redirect') }}
        v-btn.mt-5(color='purple lighten-3', href='javascript:window.history.go(-1);', outlined)
          v-icon(left) mdi-arrow-left
          span {{ $t('newpage.goback') }}
</template>

<script>
import _ from 'lodash'
import createPageMutation from 'gql/editor/create.gql'

export default {
  props: {
    locale: {
      type: String,
      default: 'en'
    },
    path: {
      type: String,
      default: 'home'
    }
  },
  data() {
    return { }
  },
  methods: {
    async redirect() {
      const returnValue = prompt('Entrer chemin de redirection : ', 'chemin de redirection')
      let resp = await this.$apollo.mutate({
        mutation: createPageMutation,
        variables: {
          content: 'Page de redirection automatique',
          description: '[redirect=' + returnValue + ']',
          editor: 'markdown',
          locale: this.locale,
          isPrivate: false,
          isPublished: true,
          path: this.path,
          publishEndDate: '',
          publishStartDate: '',
          tags: '',
          title: this.path
        }
      })
      resp = _.get(resp, 'data.pages.create', {})
      if (_.get(resp, 'responseResult.succeeded')) {
        window.location.assign(`/${this.locale}/${this.path}`)
      } else {
        throw new Error(_.get(resp, 'responseResult.message'))
      }
    }
  }
}
</script>

<style lang='scss'>

</style>

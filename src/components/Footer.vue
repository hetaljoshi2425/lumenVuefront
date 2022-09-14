<template>
  <footer :class="['bd-footer', 'text-muted', { 'docs-border': isDocs }]">
    <b-container>
      <hr />
      <p class="mb-2">
        Designed and built with all the love in the world. Maintained by the
        <a href="https://github.com/orgs/bootstrap-vue/people" target="_blank"
          >core team</a
        >
        with the help of
        <a
          href="https://github.com/bootstrap-vue/bootstrap-vue/graphs/contributors"
          target="_blank"
          >our contributors</a
        >.
      </p>

      <p>
        Currently v{{ version }}. Code licensed
        <a
          href="https://github.com/bootstrap-vue/bootstrap-vue/blob/master/LICENSE"
          target="_blank"
          >MIT</a
        >. Docs generated with
        <a href="https://nuxtjs.org/" target="_blank">Nuxt.js</a
        ><template v-if="!isNetlify && !isVercel">.</template>
        <template v-if="isNetlify">
          and proudly hosted on
          <a href="https://www.netlify.com" target="_blank">Netlify</a>.
        </template>
        <template v-else-if="isVercel">
          and proudly hosted on
          <a href="https://vercel.com/?utm_source=bootstrapvue" target="_blank"
            >Vercel</a
          >.
        </template>
      </p>

      <template v-if="isVercel">
        <p class="mt-3 text-center">
          <a
            href="https://vercel.com/?utm_source=bootstrapvue"
            target="_blank"
            rel="noopener"
          >
          </a>
        </p>
      </template>
    </b-container>
  </footer>
</template>

<script>
export default {
  name: "BVFooter",
  props: {
    isDocs: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {};
  },
  computed: {
    isNetlify() {
      return Boolean(process.env.NETLIFY);
    },
    isVercel() {
      return Boolean(process.env.VERCEL_NOW);
    },
  },
};
</script>

<style scoped>
.bv-logo {
  margin-top: -2rem;
}
.docs-border {
  border-top: 3px solid #dee2e6;
}
@media (min-width: 1200px) {
  .bd-footer > .container {
    max-width: 960px;
  }
}
</style>
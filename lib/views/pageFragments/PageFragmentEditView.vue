<template>
  <div
    v-if="!loading"
    class="create-post">
    <div class="container">
      <div class="row">
        <div class="col-md-9">
          <div class="card h-100">
            <div class="card-header">
              <h5 class="section mb-0">
                Oppdatér fragment — Innhold
              </h5>
            </div>
            <div class="card-body">
              <Villain
                :value="page.data"
                :templates="settings.templates"
                :template-mode="settings.templateMode"
                name="page[data]"
                label="Innhold"
                @input="page.data = $event" />
            </div>
          </div>
        </div>
        <div class="col-md-3">
          <div class="card">
            <div class="card-header">
              <h5 class="section mb-0">
                Oppdatér fragment — attributter
              </h5>
            </div>
            <div class="card-body">
              <KInputSelect
                v-model="page.page_id"
                :value="page.page_id"
                :options="parents"
                :has-error="errors.has('page[page_id]')"
                :error-text="errors.first('page[page_id]')"
                name="page[page_id]"
                label="Tilhørende side"
                data-vv-name="page[page_id]"
                data-vv-value-path="innerValue" />
              <KInputSelect
                v-model="page.language"
                v-validate="'required'"
                :value="page.language"
                :options="[
                  { name: 'English', value: 'en' },
                  { name: 'Norsk', value: 'no' }
                ]"
                :has-error="errors.has('page[language]')"
                :error-text="errors.first('page[language]')"
                name="page[language]"
                label="Språk"
                data-vv-name="page[language]"
                data-vv-value-path="innerValue" />

              <KInput
                v-model="page.parent_key"
                v-validate="'required'"
                :value="page.parent_key"
                :has-error="errors.has('page[parent_key]')"
                :error-text="errors.first('page[parent_key]')"
                name="page[parent_key]"
                type="text"
                label="Hovednøkkel"
                placeholder="Hovednøkkel"
                data-vv-name="page[parent_key]"
                data-vv-value-path="innerValue" />

              <KInput
                v-model="page.key"
                v-validate="'required'"
                :value="page.key"
                :has-error="errors.has('page[key]')"
                :error-text="errors.first('page[key]')"
                name="page[key]"
                type="text"
                label="Nøkkel"
                placeholder="Nøkkel"
                data-vv-name="page[key]"
                data-vv-value-path="innerValue" />

              <KInputTextarea
                v-model="page.wrapper"
                class="text-mono"
                :value="page.wrapper"
                :has-error="errors.has('page[wrapper]')"
                :error-text="errors.first('page[wrapper]')"
                name="page[wrapper]"
                type="text"
                label="HTML wrapper (avansert)"
                help-text="bruk ${CONTENT} for å hente ut fragmentets verdi"
                data-vv-name="page[wrapper]"
                data-vv-value-path="innerValue"></KInputTextarea>

              <div class="mt-4">
                <button
                  :disabled="!!loading"
                  class="btn btn-secondary btn-block"
                  @click="validate">
                  Lagre oppdatert fragment
                </button>
              </div>

              <router-link
                :disabled="!!loading"
                :to="{ name: 'pages' }"
                class="btn btn-outline-secondary btn-block mt-2">
                Tilbake til oversikten
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import nprogress from 'nprogress'
import showError from '@univers-agency/kurtz/lib/utils/showError'
import { pageFragmentAPI } from '@univers-agency/kurtz/lib/api/pageFragment'
import { alertError } from '@univers-agency/kurtz/lib/utils/alerts'
import { mapGetters } from 'vuex'

export default {
  props: {
    pageId: {
      type: [String, Number],
      required: true
    }
  },
  data () {
    return {
      loading: true,
      parents: [],
      page: null
    }
  },

  computed: {
    ...mapGetters('config', [
      'settings'
    ])
  },

  inject: [
    'adminChannel'
  ],

  async created () {
    this.getParents()
    const p = await pageFragmentAPI.getPageFragment(this.pageId)
    this.page = { ...p }
    this.loading = false
  },

  methods: {
    getParents () {
      this.adminChannel.channel
        .push('pages:list_parents')
        .receive('ok', payload => {
          this.parents = payload.parents
        })
        .receive('error', err => {
          console.log(err)
        })
    },

    validate () {
      this.$validator.validateAll().then(result => {
        if (!result) {
          alertError('Feil i skjema', 'Vennligst se over og rett feil i rødt')
          return
        }
        this.save()
      }).catch(err => {
        console.log(err)
      })
    },

    async save () {
      try {
        nprogress.start()
        await pageFragmentAPI.updatePageFragment(this.pageId, this.page)
        nprogress.done()
        this.$toast.success({ message: 'Fragment oppdatert' })
        this.$router.push({ name: 'pages' })
      } catch (err) {
        showError(err)
      }
    }
  }
}
</script>

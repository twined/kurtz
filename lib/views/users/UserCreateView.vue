<template>
  <transition
    name="fade"
    mode="out-in"
    appear>
    <div class="users-create">
      <div
        id="content"
        class="container">
        <div class="cards">
          <div class="card w-50">
            <div class="card-header">
              <h5 class="section mb-0">
                Ny bruker
              </h5>
            </div>
            <div class="card-body">
              <KInputRadios
                v-model="user.role"
                v-validate="'required'"
                :value="user.role"
                :options="[
                  { name: 'Superbruker', value: 'superuser' },
                  { name: 'Admin', value: 'admin' },
                  { name: 'Stab', value: 'staff' }
                ]"
                :has-error="errors.has('user[role]')"
                :error-text="errors.first('user[role]')"
                name="user[role]"
                label="Rolle"
                data-vv-name="user[role]"
                data-vv-value-path="innerValue" />

              <KInputSelect
                v-model="user.language"
                v-validate="'required'"
                :value="user.language"
                :options="[
                  { name: 'Norsk', value: 'nb' },
                  { name: 'Engelsk', value: 'en' }
                ]"
                :has-error="errors.has('user[language]')"
                :error-text="errors.first('user[language]')"
                name="user[language]"
                label="Språk"
                data-vv-name="user[language]"
                data-vv-value-path="innerValue" />

              <KInput
                v-model="user.full_name"
                v-validate="'required'"
                :value="user.full_name"
                :has-error="errors.has('user[full_name]')"
                :error-text="errors.first('user[full_name]')"
                name="user[full_name]"
                label="Navn"
                placeholder="Navn"
                data-vv-name="user[full_name]"
                data-vv-value-path="innerValue" />

              <KInputEmail
                v-model="user.email"
                v-validate="'required'"
                :value="user.email"
                :has-error="errors.has('user[email]')"
                :error-text="errors.first('user[email]')"
                name="user[email]"
                label="Epost"
                placeholder="Epost"
                data-vv-name="user[email]"
                data-vv-value-path="innerValue" />
              <KInputPassword
                v-model="user.password"
                v-validate="'min:6'"
                ref="password"
                :value="user.password"
                :has-error="errors.has('user[password]')"
                :error-text="errors.first('user[password]')"
                name="user[password]"
                label="Passord"
                placeholder="Passord"
                data-vv-name="user[password]"
                data-vv-value-path="innerValue" />
              <KInputPassword
                v-model="user.password_confirm"
                v-validate="'confirmed:password'"
                :value="user.password_confirm"
                :has-error="errors.has('user[password_confirm]')"
                :error-text="errors.first('user[password_confirm]')"
                name="user[password_confirm]"
                label="Bekreft passord"
                placeholder="Bekreft passord"
                data-vv-name="user[password_confirm]"
                data-vv-value-path="innerValue" />

              <button
                class="btn btn-secondary mt-4"
                @click.prevent="validateBeforeSubmit">
                Lagre ny bruker
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import { mapActions } from 'vuex'
import { alertError } from '../../utils/alerts'

export default {
  data () {
    return {
      user: {
        full_name: '',
        email: '',
        language: 'nb',
        role: 'user',
        password: '',
        password_confirm: ''
      }
    }
  },

  inject: [
    'adminChannel'
  ],

  created () {
    console.debug('created <UsersCreate />')
  },

  methods: {
    async submitForm () {
      try {
        await this.storeUser(this.user)
        this.$toast.success({ message: 'La til ny bruker' })
        this.user = {
          full_name: '',
          email: '',
          role: [],
          language: 'nb',
          password: '',
          password_confirm: ''
        }
        this.$validator.reset()
        this.$router.push({ name: 'users' })
      } catch (err) {
        throw err
      }
    },

    validateBeforeSubmit (e) {
      this.loading = true
      this.$validator.validateAll().then(result => {
        if (!result) {
          alertError('Feil i skjema', 'Vennligst se over og rett feil i rødt')
          this.loading = false
          return
        }
        this.submitForm()
        this.loading = false
      })
    },
    ...mapActions('users', [
      'storeUser'
    ])
  }
}
</script>

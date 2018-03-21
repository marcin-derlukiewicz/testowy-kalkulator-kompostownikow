<template>
    <v-flex justify-center align-center>
        <v-form v-if="showForm" v-model="valid" ref="form">
            <v-text-field label="Ilość gospodarstw domowych" v-model="aptNumber" :rules="aptNumberRules" required></v-text-field>
            <v-text-field label="Średnia ilość osób w gospodarstwie" v-model="pplNumber" :rules="pplNumberRules" required></v-text-field>
            <v-btn @click.stop="showDialogMethod" :loading="showLoading">Oblicz</v-btn>
        </v-form>
        <h1 v-if="computedResponse !== false" > Potrzebujesz {{computedResponse}} kompostowników o pojemności {{rozmiarKompostownika}} litrów </h1>
        <v-dialog v-model="showDialog" max-width="500px">
            <v-card>
                <v-card-text>
                    <p>Wprowadź adres email aby zapisać się na newsletter. Szczegóły <nuxt-link to="/email-details">tutaj</nuxt-link>. Wcisnięcie guzika wyślij oznacza akceptację warunków.</p>
                    <v-form v-model="emailFormValid" ref="emailForm" >
                      <v-text-field label="Wprowadź adres email" v-model="email" :rules="emailRules" ></v-text-field>
                    </v-form>
                </v-card-text>
                <v-card-actions>
                        <v-btn color="primary" dark @click.stop="submitEmail" :loading="showEmailLoading">Zapisz się</v-btn>
                        <v-spacer></v-spacer>
                        <v-btn color="primary" flat @click.stop="submitForm" v-if="!showEmailLoading">Nie, dzięki, zabierz mnie do wyników</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-flex>
</template>

<script>
import axios from 'axios'
export default {
  data: () => ({
    showForm: true,
    showLoading: false,
    showEmailLoading: false,
    showDialog: false,
    valid: false,
    aptNumber: 30,
    aptNumberRules: [
      v => /\d+/.test(v) || 'Liczba gospodarstw jest niepoprawna'
    ],
    pplNumber: 3.8,
    pplNumberRules: [
      v => /\d+(\.\d+){0,1}/.test(v) || 'Liczba gospodarstw jest niepoprawna'
    ],
    emailFormValid: false,
    email: '',
    emailRules: [
      v => !!v || 'E-mail is required',
      v => /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
    ],
    stalaS: 60,
    rozmiarKompostownika: 30,
    tablicaObjetosci: {
      0: 0,
      1: 0.5,
      2: 0.6,
      3: 0.8,
      4: 1,
      5: 1,
      6: 1.2
    },
    computedResponse: false
  }),
  methods: {
    calculateCompostSize () {
      if (this.tablicaObjetosci[Math.round(this.pplNumber)] !== undefined) {
        return Math.ceil(this.stalaS * this.tablicaObjetosci[Math.round(this.pplNumber)] * this.aptNumber / this.rozmiarKompostownika)
      } else {
        return false
      }
    },
    showDialogMethod () {
      // uncomment to alwaus show emai subscribe dialog
      // this.$cookies.set('emailSubscribed', false) // debug
      if (this.$cookies.get('emailSubscribed')) {
        // usability hack
        this.showLoading = true
        setTimeout(() => (this.submitForm()), 700)
      } else {
        this.showForm = false
        this.showDialog = true
      }
    },
    submitForm () {
      this.showLoading = false
      this.showDialog = false
      this.showForm = true
      this.computedResponse = this.calculateCompostSize()
    },
    submitEmail () {
      if (this.$refs.emailForm.validate()) {
        this.showEmailLoading = true
        setTimeout(() => (
          // change get to post and provide params
          axios.get('http://localhost:3000/api/subscribe.json', {
            email: this.email
          }).then((response) => {
            this.$cookies.set('emailSubscribed', true, {
              path: '/',
              maxAge: 60 * 60 * 24 * 7
              // add more options for security
            })
            this.submitForm()
          }).catch((error) => {
            // todo iterpret errors from service
            console.log(error)
          })
        ), 1700) // ux hack instead of real sending data, to remove when service implemented
      }
    }
  }
}
</script>

<style>

</style>

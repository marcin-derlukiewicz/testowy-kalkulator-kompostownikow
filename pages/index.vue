<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm12 md10>
      <div class="text-xs-center">
        <img src="/v.png" alt="Vuetify.js" class="mb-5" />
      </div>
      <blockquote class="blockquote">
        &#8220;First, solve the problem. Then, write the code.&#8221;
        <footer>
          <small>
            <em>&mdash;John Johnson</em>
          </small>
        </footer>
      </blockquote>
      <Calc />
    </v-flex>
  </v-layout>
</template>

<script>
import Calc from '~/components/Calc'

export default {
  components: {
    Calc
  }
}
</script>


<script lang="ts">
import Vue from 'vue';
import NiceNumericInput from '@/nice-numeric-input.vue';

export default Vue.extend({
  name: 'ServeDev',
  components: {
    NiceNumericInput
  },
  data: () => ({
    test: 0
  })
});
</script>

<template>
  <div id="app">
    <div style="width: 10%">
      <nice-numeric-input v-model="test" placeholder="test" label="test label" name="testnumber" currency="GBP" locale="en-GB" :max="100" :min="0" />
    </div>
    <div>
      {{test}}
    </div>
  </div>
</template>

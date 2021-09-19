<script lang="ts">
import Vue from 'vue';
import NiceNumericInput from '@/nice-numeric-input.vue';

export default Vue.extend({
  name: 'ServeDev',
  components: {
    NiceNumericInput
  },
  data: () => ({
    cashValue: 0
  })
});
</script>

<template>
  <div id="app">
    <div style="width: 10%">
      <nice-numeric-input v-model="cashValue"
                          placeholder="Enter a cash value"
                          label="Cash"
                          name="example"
                          currency="GBP"
                          integer-only
                          :max="100"
                          :min="0"
                          hide-label
                          no-controls />
    </div>
    <div>
      {{cashValue}}
    </div>
  </div>
</template>

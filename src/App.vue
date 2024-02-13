
<template>
  <div :class="bemm()">

    <h1><strong>VAT</strong>Calculator</h1>

    <div :class="bemm('input')">
      <FormField>
        <InputNumber label="amount" v-model="amount" />
        <FormGroup>
          <InputNumber label="percentage" v-model="percentage" />
          <InputSwitch v-model="direction" :values="['add', 'extract']" />
        </FormGroup>
      </FormField>
    </div>

    <div :class="bemm('result')" v-if="amount !== 0 || percentage !== 0">
      <div :class="[bemm('column'), bemm('incl')]" v-if="direction == 'add'">
        <h3>Add</h3>
        <dl>
          <dt>Price</dt>
          <dd>{{ amount }}</dd>
        </dl>
        <dl>
          <dt>VAT Percentage</dt>
          <dd>{{ percentage }}</dd>
        </dl>

        <dl>
          <dt>Total</dt>
          <dd>{{ inclusive.total }}</dd>
        </dl>

        <dl>
          <dt>VAT</dt>
          <dd>{{ inclusive.vat }}</dd>
        </dl>
        <hr />

        <p>{{ amount }} + {{ percentage }}%</p>


      </div>
      <div :class="[bemm('column'), bemm('excl')]" v-if="direction == 'extract'">
        <h3>Extract</h3>


        <dl>
          <dt>Price</dt>
          <dd>{{ amount }}</dd>
        </dl>
        <dl>
          <dt>VAT Percentage</dt>
          <dd>{{ percentage }}</dd>
        </dl>
        <dl>
          <dt>Total</dt>
          <dd>{{ exclusive.total }}</dd>
        </dl>

        <dl>
          <dt>VAT</dt>
          <dd>{{ exclusive.vat }}</dd>
        </dl>
        <hr />
        <p>

          {{ amount }} - {{ percentage }}% = {{ exclusive.total }}
        </p>

      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue';
import { FormField, FormGroup, InputNumber, InputSwitch } from './components/form';
import { useBemm } from 'bemm';
const bemm = useBemm('app');
const amount = ref(1000);
const percentage = ref(20);
const direction = ref<'add' | 'extract'>('add');

const pad = (n: number, afterComma = 2) => {

  return Math.round(n * Math.pow(10, afterComma)) / Math.pow(10, afterComma)
}


const inclusive = computed(() => {
  const total = amount.value + (amount.value * percentage.value) / 100;
  return {
    total: pad(total),
    vat: pad(total - amount.value)
  }
});

const exclusive = computed(() => {
  const total = Math.round((amount.value / (1 + percentage.value / 100)) * 100) / 100

  return {
    total: pad(total),
    vat: pad(amount.value - total)
  }
});


// import HelloWorld from './components/HelloWorld.vue'
</script>

<style lang="scss">
.app {
  display: flex;
  flex-direction: column;
  gap: var(--space);
  padding: var(--space);


  &__input {

    background-color: black;
    border-radius: var(--border-radius);
  }

  &__result {
    display: flex;
    justify-content: space-evenly;
    gap: var(--space);
    width: 100%;
  }

  &__incl {}

  &__excl {}

  &__column {
    padding: var(--space);
    // background-color: var(--foreground);
    // color: var(--background);

    background-color: var(--primary);
    color: var(--primary-text);
    width: 100%;
    border-radius: var(--border-radius);
  }

  dl {
    display: flex;
    width: 100%;
    justify-content: space-evenly;

    &+dl {
      margin-top: calc(var(--space) / 2);
    }

    dt,
    dd {
      text-align: left;
      width: 100%;
    }

    dd {
      font-weight: bold;
    }
  }
  hr {
    margin-top: var(--space);
    background-color: currentColor;
    border: none;
    color: currentColor;
    height: 1px;
  }
  hr+p{
    opacity: .5;margin-top: var(--space);
  }

}
</style>


<template>
  <div :class="bemm()">

    <h1><strong>VAT</strong>Calculator</h1>



    <div :class="bemm('input')">
      <FormField>
        <InputSwitch v-model="calcType" :options="calculationOptions" />
      </FormField>
      <FormField>
        <FormGroup v-if="calcType == CalcType.PERCENTAGE">
          <InputNumber label="Amount" v-model="amount"  />
          <InputNumber label="of Amount" v-model="amount2" />
        </FormGroup>
        <FormGroup v-else-if="calcType == CalcType.VAT">
          <InputNumber label="Amount" v-model="amount" :style="`--input-control-font-size: 2em`" />
        </FormGroup>
        <FormGroup v-if="calcType == CalcType.VAT">
          <InputNumber label="Percentage" v-model="percentage" />
          <InputSwitch v-model="direction" :options="directionOptions" />
        </FormGroup>
      </FormField>
    </div>

    <div :class="bemm('result')" v-if="calcType == CalcType.PERCENTAGE">
      <div :class="bemm('column')">

        {{ amount }} is <strong>{{ calculatedPercentage }}%</strong> of {{ amount2 }}

      </div>
    </div>
    <div :class="bemm('result')" v-if="calcType == CalcType.VAT && (amount !== 0 || percentage !== 0)">
      <div :class="[bemm('column'), bemm('incl')]" v-if="direction == 'add'">
        <h3>Add</h3>

        <div :class="bemm('amount')">
          {{ formatCurrency(inclusive.total) }}
        </div>
        <dl>
          <dt>Price</dt>
          <dd>{{ formatCurrency(amount) }}</dd>
        </dl>
        <dl>
          <dt>VAT Percentage</dt>
          <dd>{{ percentage }}%</dd>
        </dl>

        <dl>
          <dt>Total</dt>
          <dd>{{ formatCurrency(inclusive.total) }}</dd>
        </dl>

        <dl>
          <dt>VAT</dt>
          <dd>{{ formatCurrency(inclusive.vat) }}</dd>
        </dl>
        <hr />

        <p>{{ formatCurrency(amount) }} + {{ percentage }}% = {{ formatCurrency(inclusive.total) }}</p>


      </div>
      <div :class="[bemm('column'), bemm('excl')]" v-if="direction == 'extract'">
        <h3>Extract</h3>


        <div :class="bemm('amount')">
          {{ formatCurrency(exclusive.total) }}
        </div>

        <dl>
          <dt>Price</dt>
          <dd>{{ formatCurrency(amount) }}</dd>
        </dl>
        <dl>
          <dt>VAT Percentage</dt>
          <dd>{{ percentage }}%</dd>
        </dl>
        <dl>
          <dt>Total</dt>
          <dd>{{ formatCurrency(exclusive.total) }}</dd>
        </dl>

        <dl>
          <dt>VAT</dt>
          <dd>{{ formatCurrency(exclusive.vat) }}</dd>
        </dl>
        <hr />
        <p>

          {{ formatCurrency(amount) }} - {{ percentage }}% = {{ formatCurrency(exclusive.total) }}
        </p>

      </div>
    </div>

    <div :class="bemm('settings')">

      <FormField>
        <InputSelect v-if="calcType == CalcType.VAT" label="Currency" v-model="currency" :options="currencies" />
      </FormField>
    </div>
  </div>
</template>
<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import { FormField, FormGroup, InputNumber, InputSwitch, InputSelect } from '@/components/form';
import { SwitchOption } from '@/components/form/types';
import { useBemm } from 'bemm';

import localeCurrency from 'locale-currency';
import { Icons } from 'open-icon';

const CalcType = {
  VAT: 'vat',
  PERCENTAGE: 'percentage'
} as const;
type CalcType = typeof CalcType[keyof typeof CalcType];



const bemm = useBemm('app');
const amount = ref(1000);
const amount2 = ref(1000);
const percentage = ref(20);
const currency = ref('EUR');
const direction = ref<'add' | 'extract'>('add');
const calcType = ref<CalcType>(CalcType.VAT);

const directionOptions: SwitchOption[] = [
  { value: 'add', label: 'Add', icon: Icons.PLUS_CIRCLE },
  { value: 'extract', label: 'Extract', icon: Icons.MINUS_CIRCLE }
]

const calculationOptions: SwitchOption[] = [
  { value: 'vat', label: 'Get VAT' },
  { value: 'percentage', label: 'Get Percentage' }
]

const pad = (n: number, afterComma = 2) => {

  return Math.round(n * Math.pow(10, afterComma)) / Math.pow(10, afterComma)
}


const formatCurrency = (n: number) => {
  return new Intl.NumberFormat(undefined, {
    style: 'currency',
    currency: currency.value
  }).format(n)
}

const currencies = [{
  value: 'EUR',
  label: 'Euro'
}, {
  value: 'USD',
  label: 'US Dollar'
}, {
  value: 'GBP',
  label: 'British Pound'
}, {
  value: 'JPY',
  label: 'Japanese Yen'
}, {
  value: 'CNY',
  label: 'Chinese Yuan'
}, {
  value: 'RUB',
  label: 'Russian Ruble'
}, {
  value: 'CHF',
  label: 'Swiss Franc'
}, {
  value: 'AUD',
  label: 'Australian Dollar'
}, {
  value: 'CAD',
  label: 'Canadian Dollar'
}, {
  value: 'SEK',
  label: 'Swedish Krona'
}, {
  value: 'NOK',
  label: 'Norwegian Krone'
}, {
  value: 'DKK',
  label: 'Danish Krone'
}, {
  value: 'PLN',
  label: 'Polish Zloty'
}, {
  value: 'CZK',
  label: 'Czech Koruna'
}, {
  value: 'HUF',
  label: 'Hungarian Forint'
}, {
  value: 'BGN',
  label: 'Bulgarian Lev'
}, {
  value: 'AMD',
  label: 'Armenian Dram'
}];



onMounted(() => {
  const userLocale = navigator.language;
  const lCurrency = localeCurrency.getCurrency(userLocale);
  if (lCurrency) {
    currency.value = lCurrency;
  }
});



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


const calculatedPercentage = computed(() => {


  return pad((amount.value / amount2.value) * 100)

})

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

  &__settings {
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
    height: 3px;
    opacity: 1;
  }

  hr+p {
    opacity: .5;
    margin-top: var(--space);
  }

  &__amount {
    background-color: var(--foreground);
    color: var(--background);
    padding: var(--space);
    border-radius: var(--border-radius);
    font-size: 1.5em;
    text-align: center;
    margin-block: var(--space);
    font-weight: bold;
  }

}
</style>

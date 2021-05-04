
<template>
  <div class="main">
    <div>

      <h1>Currency Exchange</h1>
      <div class="notice">Note: The only from currency that works with the dev version of Swop API is EUR</div>

      <div class="from">
        <input v-model="state.fromAmount" type="number" placeholder="Input Amount ex: 1.00">
        <select v-model="state.fromCurrency">
          <option value="" disabled>From Currency</option>
          <option v-for="(value, key) in state.currencyList" :value="key">{{ value }}</option>
        </select>
      </div>

      <div class="add-currency">
        <select v-model="state.toCurrency">
          <option value="" disabled>To Currency</option>
          <option v-for="(value, key) in state.currencyList" :value="key">{{ value }}</option>
        </select>
        <button @click="addCurrency()">Add Currency</button>
      </div>

      <div class="currency-list">
        <h2>Currencies to convert:</h2>
        <div v-if="state.toCurrencyList.length === 0">Add currencies to convert...</div>
        <div v-for="(item, index) in state.toCurrencyList">
          <span>{{ item }}</span>
          <div></div>
          <!-- temporary styles -->
          <div style="display: flex; justify-content: flex-end;">
            <button @click="removeCurrency(index)" class="-remove">Remove</button>
          </div>
        </div>
      </div>

      <div class="ribbon">
        <button @click="fetchExchangeRates()">Fetch Exchange Rates</button>
      </div>

      <div class="currency-list">
        <h2>Latest exchange rates:</h2>
        <!-- TODO: replace loading text with a spinner -->
        <div v-if="state.exchangeListLoading === true">Loading...</div>
        <div v-else-if="state.exchangeList.length === 0">Add currencies to convert...</div>
        <div v-for="(item, index) in state.exchangeList">
          <span>{{ item.quoteCurrency }}</span>
          <span>Currency: {{ Math.round(item.quote * state.fromAmount * 100) / 100 }}</span>
          <span>Quote: {{ item.quote }}</span>
        </div>
      </div>

      <!-- <div class="ribbon">
        <button @click="saveExchangeRates()">Save Exchange Rates</button>
      </div> -->

    </div>
  </div>
</template>

<script setup>

import { reactive, onMounted } from 'vue'

const state = reactive({
  fromAmount: null,
  fromCurrency: '',

  toCurrency: '',
  toCurrencyList: [],

  exchangeList: [],
  exchangeListLoading: false,

  currencyListRestored: false,
  currencyList: [],

  persist: reactive({
    currencyListRaw: null
  })
})

/**
 *
 * Methods
 *
 */

const addCurrency = () => {
  const { toCurrency, toCurrencyList } = state

  if (toCurrency === '' || toCurrencyList.includes(toCurrency)) {
    // do nothing
    // condition is easier to read this way rather than making logic inversed
  } else {
    toCurrencyList.push(toCurrency)
  }
}

const removeCurrency = index => {
  state.toCurrencyList.splice(index, 1)
}

/**
 *
 * Helpers / Utils
 *
 */

const persistData = data => {
  localStorage.setItem('persist-swop', JSON.stringify(state.persist))
}

const formatCurrencyList = data => {
  const result = {}

  for (let i = 0; i < data.length; i++) {
    const { name, code } = data[i]
    result[code] = name
  }

  return result
}

/**
 *
 * Swop API requests
 * https://swop.cx/documentation/graphql
 * https://swop.cx/account/dashboard/playground
 *
 */

const fetchCurrencyList = () => {
  // Guard check to prevent needless API request
  // Swop only allows 1000 requests per month on the developer plan
  if (state.currencyListRestored === true) {
    console.log('>>> [fetchCurrencyList] data was already restored from local storage')
    return // do nothing
  }

  fetch('https://swop.cx/graphql', {
    method: 'POST',
    headers: {
      Authorization: 'ApiKey 0f218aaf671eeb5e94fba3ce675f7d0fcc552e3a1a920878859dccbff106e44b'
    },
    body: JSON.stringify({
      query: `
query {
  currencies {
    name
    code
  }
}`
    })
  })
    .then(res => res.json())
    .then(data => {
      const { currencies } = data.data

      // Add currencies to state
      state.persist.currencyListRaw = currencies
      state.currencyList = formatCurrencyList(currencies)

      // Update persisted data
      persistData(state.persist)
    })
}

const fetchExchangeRates = () => {
  const baseCurrency = state.fromCurrency
  const quoteCurrencies = `["${state.toCurrencyList.join('", "')}"]`

  state.exchangeListLoading = true

  fetch('https://swop.cx/graphql', {
    method: 'POST',
    headers: {
      Authorization: 'ApiKey 0f218aaf671eeb5e94fba3ce675f7d0fcc552e3a1a920878859dccbff106e44b'
    },
    body: JSON.stringify({
      query: `
query {
  latest(baseCurrency: "${baseCurrency}", quoteCurrencies: ${quoteCurrencies}) {
    quoteCurrency
    quote
  }
}`
    })
  })
    .then(res => res.json())
    .then(data => {
      const { latest } = data.data
      state.exchangeList = latest
      state.exchangeListLoading = false
    })
    .catch(error => {
      // This should be a modal or something
      alert('Please upgrade your account to convert from this currency.')
      console.log(error)
    })
}

const saveExchangeRates = () => {
  alert('Soon...')

  // fetch('', {
  //   method: 'POST',
  //   body: JSON.stringify({
  //   })
  // })
  //   .then(res => res.json())
  //   .then(data => {
  //   })
  //   .catch(error => {
  //   })
}

/**
 *
 * Lifecycle events
 *
 */

onMounted(() => {
  const data = localStorage.getItem('persist-swop')

  if (data != null) {
    state.persist = JSON.parse(data)
    state.currencyListRestored = true
    console.log('>>> [restoreData] data has been restored')
  } else {
    fetchCurrencyList()
  }
})

</script>

<style lang="scss">
  h1 {
    font: 600 36px/1 sans-serif;
  }

  h2 {
    font: 600 24px/2 sans-serif;
  }

  input,
  select {
    padding: 0 12px;
    border: 2px solid transparent;
    border-radius: 6px;
    outline: none;
    background: var(--dark-300);
  }

  input {
    font: 16px/36px sans-serif;
  }

  select {
    width: 100%;
  }

  input:focus,
  select:focus {
    border: 2px solid var(--primary);
  }

  button {
    padding: 0 18px;
    border-radius: 6px;
    font: 14px/40px sans-serif;
    background: var(--primary);
    cursor: pointer;

    &.-remove {
      padding: 0 12px;
      font: 12px/24px sans-serif;
      background: var(--red);
    }
  }

  /**
  *
  * Layout
  *
  */

  .main {
    display: grid;

    > div {
      display: grid;
      grid: auto / 1fr;
      gap: 24px;
      max-width: 640px;
      margin: 128px auto;
      padding: 36px;
      border-radius: 12px;
      background: var(--dark-400);
    }
  }

  .notice {
    padding: 12px 18px;
    border-left: 2px solid var(--red);
    border-radius: 3px 6px 6px 3px;
    font: 12px/1 sans-serif;
    background: var(--dark-300);
  }

  .from {
    display: grid;
    grid: auto / 1fr 1fr;
    gap: 12px;
  }

  .add-currency {
    display: grid;
    grid: auto / 1fr auto;
    gap: 12px;
  }

  .currency-list {
    > div {
      display: grid;
      grid: auto / 1fr 1fr 1fr;
      padding: 12px 24px;
      font: 14px/24px sans-serif;

      &:nth-child(even) {
        border-radius: 6px;
        background: var(--dark-300);
      }
    }
  }

  .ribbon {
    display: grid;

    button {
      margin: auto;
    }
  }
</style>

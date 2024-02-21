<template>
	<div class="wrapper">
		<h1>Exchange Rate App</h1>
		<p>Sprawdź aktualne kursy walut!</p>

		<div class="app-body">
			<div class="section-left">
				<input
					v-model="amountOne"
					@input="calculate"
					@keydown.up.prevent="calculate"
					@keydown.down.prevent="calculate"
					type="number"
					class="amount-one"
					value="1" />
				<select id="currency-one" v-model="currencyOne" @change="calculate">
					<option value="PLN" selected>PLN</option>
					<option value="USD">USD</option>
					<option value="GBP">GBP</option>
					<option value="EUR">EUR</option>
					<option value="CHF">CHF</option>
				</select>
			</div>
			<button class="swap" @click="swap">
				<i class="fas fa-retweet"></i>
			</button>
			<div class="section-right">
				<input type="number" :value="test" class="amount-two" disabled />
				<select id="currency-two" v-model="currencyTwo" @change="calculate">
					<option value="PLN">PLN</option>
					<option value="USD" selected>USD</option>
					<option value="GBP">GBP</option>
					<option value="EUR">EUR</option>
					<option value="CHF">CHF</option>
				</select>
			</div>
		</div>
		<p class="rate-info" v-if="infoStatus">
			{{ rateInfo }}
		</p>
	</div>
</template>

<script setup>
import { ref, computed } from 'vue'
import axios from 'axios'

let amountOne = ref(1)
let amountTwo = ref(0)
const currencyOne = ref('PLN')
const currencyTwo = ref('USD')
const foundRate = ref()
const infoStatus = ref(false)

const test = ref()

const API_KEY = ref('cb0b3f0f516f282f5b19b8fa')

const calculate = async () => {
	infoStatus.value = true

	try {
		const response = await axios.get(`https://v6.exchangerate-api.com/v6/${API_KEY.value}/latest/${currencyOne.value}`)
		const rates = response.data.conversion_rates
		foundRate.value = rates[currencyTwo.value]
		let amountN1 = parseFloat(amountOne.value)
		let amountN2 = parseFloat(amountTwo.value)
		amountN2 = amountN1 * foundRate.value
		test.value = amountN2.toFixed(2)
	} catch (error) {
		console.log(error)
	}
}

const swap = () => {
	const oldValue = currencyOne.value
	currencyOne.value = currencyTwo.value
	currencyTwo.value = oldValue
	calculate()
}

const rateInfo = computed(() => {
	return `1 ${currencyOne.value} = ${foundRate.value} ${currencyTwo.value}`
})
</script>

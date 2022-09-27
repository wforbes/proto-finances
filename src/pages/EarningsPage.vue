<template>
	<q-page>
		<div class="row q-px-lg">
			<div class="column" style="width: 100%">
				<LineChart :chartData="testData" :options="lineOptions" />
			</div>
		</div>
		<div class="row justify-center">
			<NewEarningsForm />
		</div>
	</q-page>
</template>
<script>
import NewEarningsForm from "src/components/NewEarningsForm.vue";
import { LineChart } from "vue-chart-3";
import { Chart, registerables } from "chart.js";
Chart.register(...registerables);
export default {
	name: "EarningsPage",
	components: {
		NewEarningsForm,
		LineChart,
	},
	data() {
		return {
			testData: {
				labels: [],
				datasets: [
					{
						label: "Historical Estimate",
						data: [
							0, 0, 2750, 5000, 13750, 13750, 14625, 14625, 16250,
							16250, 16250, 16250, 16875, 16875, 17500, 17500,
							20000, 20000, 21010,
						],
						clip: { top: 15, left: 0, right: 0, bottom: 0 },
						fill: false,
						borderColor: "rgb(75, 192, 192)",
						tension: 0,
					},
				],
			},
			lineOptions: {
				scales: {
					y: {
						suggestedMax: 30000,
						suggestedMin: 0,
					},
				},
			},
		};
	},
	created() {
		this.testData.labels = this.getLabels();
	},
	computed: {
		labels() {
			return this.getLabels();
		},
	},
	methods: {
		getLabels() {
			const startYr = 18;
			const endYr = 23;
			let out = [];
			for (let yr = startYr; yr < endYr + 1; yr++) {
				let year = "" + yr;
				let quarter;
				for (let q = 1; q <= 4; q++) {
					quarter = "Q" + q;
					out.push(`${quarter} ${year}`);
				}
			}
			return out;
		},
	},
};
</script>

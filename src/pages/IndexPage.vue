<template>
	<q-page>
		<div class="row justify-center">
			<h1 class="text-h4">Checking Transaction Import</h1>
		</div>
		<div class="row justify-center q-ma-lg">
			<div class="col-6">
				<q-select
					v-model="importTarget"
					:options="importOptions"
					label="Import Type"
				></q-select>
			</div>
		</div>
		<div class="row justify-center q-ma-lg">
			<div class="offset-3 col-6 justify-center">
				<component
					v-if="importTargetDefined"
					:is="importTarget.value"
					@returnData="setTransactions"
				/>
			</div>
		</div>
		<div class="row justify-center q-mt-lg">
			<q-btn @click="showRawData" :disabled="transactions.length == 0">
				View Raw Data
			</q-btn>
			<q-dialog v-model="rawDataOpen">
				<q-card class="">
					<div class="row q-ma-lg">
						<div
							v-for="(t, i) of transactions"
							:key="i"
							class="row q-my-md"
						>
							<div v-for="(f, j) of Object.keys(t)" :key="j">
								<div class="column">{{ f }} = {{ t[f] }}</div>
							</div>
						</div>
					</div>
				</q-card>
			</q-dialog>
		</div>
		<div class="row q-mt-lg">
			<div class="col-12 q-px-lg">
				<q-table :columns="columns" :rows="transactions">
					<template v-slot:body-cell-postingDate="props">
						<q-td :props="props">
							{{ formatDate(props.row.postingDate) }}
						</q-td>
					</template>
				</q-table>
			</div>
		</div>
	</q-page>
</template>

<script>
import { date } from "quasar";
import ChaseImporter from "src/components/ChaseImporter.vue";

export default {
	name: "IndexPage",
	components: {
		ChaseImporter,
	},
	data() {
		return {
			importTarget: "",
			importOptions: [
				{
					label: "JPM Chase '.csv' File",
					value: "ChaseImporter",
				},
			],
			transactions: [],
			columns: [],
			rawDataOpen: false,
		};
	},
	computed: {
		importTargetDefined() {
			return this.importTarget !== "";
		},
	},
	methods: {
		showRawData() {
			if (this.transactions.length === 0) return;
			this.rawDataOpen = true;
		},
		setTransactions(transactions) {
			let headers = Object.keys(transactions[0]);

			for (let header of headers) {
				if (header === "Posting Date") {
					header = this.camelCase(header);
				}
				this.columns.push({
					name: header,
					label: header,
					field: header,
					sortable: true,
					align: "left",
				});
			}

			//rename key 'Posting Date' to 'postingDate'
			//	and turn it into a Date object
			for (let tran of transactions) {
				Object.defineProperty(
					tran,
					"postingDate",
					Object.getOwnPropertyDescriptor(tran, "Posting Date")
				);
				delete tran["Posting Date"];
				tran.postingDate = new Date(tran.postingDate);
			}

			this.transactions = transactions;
		},
		formatDate(dateObj) {
			console.log(dateObj);
			return date.formatDate(dateObj, "MM-DD-YY");
			//return "k";
		},
		camelCase(str) {
			return this.lcFirst(str.split(" ").join(""));
		},
		lcFirst(str) {
			return str.charAt(0).toLowerCase() + str.slice(1);
		},
	},
};
</script>

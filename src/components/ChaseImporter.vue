<template>
	<div class="row">
		<q-file
			filled
			bottom-slots
			v-model="file"
			label="Transaction CSV File"
			counter
		>
			<template v-slot:prepend>
				<q-icon name="mdi-file-delimited" @click.stop.prevent />
			</template>
			<template v-slot:append>
				<q-icon
					name="close"
					@click.stop.prevent="file = null"
					class="cursor-pointer"
				/>
			</template>
			<template v-slot:hint> Transaction CSV </template>
		</q-file>
		<q-btn
			@click="importFile"
			color="primary"
			label="Import"
			style="height: 25px"
		/>
	</div>
</template>
<script>
export default {
	name: "ChaseImporter",
	data() {
		return {
			file: undefined,
		};
	},
	methods: {
		importFile(event) {
			const fileReader = new FileReader();
			fileReader.onload = (e) => {
				const fileText = e.target.result;
				const objs = this.csvToJSON(fileText);
				if (objs.length > 0) {
					this.passTransactions(objs);
				}
			};
			fileReader.readAsText(this.file);
		},
		csvToJSON(csv) {
			let lines = csv.split("\n");
			let result = [];
			let headers = lines[0].split(",");
			for (let i = 1; i < lines.length; i++) {
				let obj = {};
				// replace any commas within double quotes
				//	so that it doesn't get split in the middle of what should be a field value
				//	like "CIT BANK, N.A. TRANSFER FORBES WILLIAM WEB ID: 124084834"
				lines[i] = lines[i].replace(
					/,(?=[^"]*"[^"]*(?:"[^"]*"[^"]*)*$)/,
					""
				);
				let currentLine = lines[i].split(",");
				for (let j = 0; j < headers.length; j++) {
					obj[headers[j]] = currentLine[j];
				}
				result.push(obj);
			}
			return result;
		},
		passTransactions(transactions) {
			this.$emit("returnData", transactions);
		},
	},
};
</script>

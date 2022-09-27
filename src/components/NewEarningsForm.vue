<template>
	<q-form class="q-pt-md">
		<div class="row">
			<q-input
				v-model="newEarnings.name"
				label="New Earnings Name"
				outlined
				class="q-mr-md"
			/>
			<q-input v-model="newEarnings.startDate" mask="date" outlined>
				<template v-slot:append>
					<q-icon name="event" class="cursor-pointer">
						<q-popup-proxy
							cover
							transition-show="scale"
							transition-hide="scale"
						>
							<q-date v-model="newEarnings.startDate">
								<div class="row items-center justify-end">
									<q-btn
										v-close-popup
										label="Close"
										color="primary"
										flat
									/>
								</div>
							</q-date>
						</q-popup-proxy>
					</q-icon>
				</template>
			</q-input>
		</div>
	</q-form>
</template>
<script>
export default {
	data() {
		return {
			newEarnings: {
				name: "",
				date: undefined,
			},
		};
	},
};
</script>

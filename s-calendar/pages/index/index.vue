<template>
	<view class="content">
		<sCalendar @monthChanged='monthChanged' @dateChanged='dateChanged'></sCalendar>
	</view>
</template>

<script>
	import sCalendar from './s-calendar.vue'
	export default {
		components: {
			sCalendar
		},
		data() {
			return {
				title: 's-calendar'
			}
		},
		onLoad() {

		},
		methods: {
			monthChanged(date) {
				console.log(new Date(date).toLocaleDateString())
			},
			dateChanged(date) {
				console.log(new Date(date).toLocaleDateString())
			}

		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: top;
	}



	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>

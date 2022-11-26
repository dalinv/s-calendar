<template>
	<view class="column">
		<view class="column" style="height: 88px;">
			<view style="font-size: 36px;">{{currentMonth.getMonth() + 1}}月</view>
			<view style="margin-bottom: 20px;">{{currentMonth.getFullYear()}}年</view>
		</view>

		<view class="calendar" :style="{height:(swiperCalendar[current].length * rowHeight  + rowHeight) + 'rpx'}">
			<view class="row">
				<view class="header" v-for="(item, index) in weekheader" :key="index">{{item}}</view>
			</view>

			<swiper circular @change="changeCalendar" :current="current"
				:style="{height:(swiperCalendar[current].length * rowHeight) + 'rpx'}">
				<swiper-item>
					<view class="row" v-for="(item, index) in swiperCalendar[0]" :key="index">
						<view class="column" v-for="(date,key) in item" :key="key" @click="clickDate(date, index)"
							:class="[selectedDate == date ? 'select-day' : 'no-select-day']">
							<view class="date"
								:class="[date.getMonth() == currentMonth.getMonth() ? 'normal' : 'disabled']">
								{{date.getDate()}}
							</view>

							<view class="zh-month-text"
								:class="[date.getMonth() == currentMonth.getMonth() ? 'normal' : 'disabled']"
								v-if="date.getDate() == 1">
								{{getZhMonth(date)}}
							</view>
							<view v-else></view>

						</view>
					</view>
				</swiper-item>


				<swiper-item>
					<view class="row" v-for="(item, index) in swiperCalendar[1]" :key="index">
						<view class="column" @click="clickDate(date, index)" v-for="(date,key) in item" :key="key"
							:class="[selectedDate == date ? 'select-day' : 'no-select-day']">
							<view class="date"
								:class="[date.getMonth() == currentMonth.getMonth() ? 'normal' : 'disabled']">
								{{date.getDate()}}
							</view>
							<view class="zh-month-text"
								:class="[date.getMonth() == currentMonth.getMonth() ? 'normal' : 'disabled']"
								v-if="date.getDate() == 1">
								{{getZhMonth(date)}}
							</view>
							<view v-else></view>
						</view>
					</view>
				</swiper-item>

				<swiper-item>
					<view class="row" v-for="(item, index) in swiperCalendar[2]" :key="index">
						<view class="column" v-for="(date,key) in item" :key="key" @click="clickDate(date, index)"
							:class="[selectedDate == date ? 'select-day' : 'no-select-day']">
							<view class="date"
								:class="[date.getMonth() == currentMonth.getMonth() ? 'normal' : 'disabled']">
								{{date.getDate()}}
							</view>
							<view class="zh-month-text"
								:class="[date.getMonth() == currentMonth.getMonth() ? 'normal' : 'disabled']"
								v-if="date.getDate() == 1">
								{{getZhMonth(date)}}
							</view>
							<view v-else></view>
						</view>
					</view>
				</swiper-item>
			</swiper>
		</view>
	</view>
</template>


<script>
	/* Props:
		weekStartDay:设置周开始时间 
		headerMode:标题显示的是：星期一/周一/一
		language:显示中文or英文
		
	   Events:
	    dateChanged:选择日期发生改变时
		monthChanged:切换月份
	*/

	import dayjs from 'dayjs'

	const WEEKDAYS = [0, 1, 2, 3, 4, 5, 6]
	const DOUBLE_WEEKDAYS = WEEKDAYS.concat(WEEKDAYS)

	const zh = {
		weekdaysFull: ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'],
		weekdaysShort: ['周日', '周一', '周二', '周三', '周四', '周五', '周六'],
		weekdaysAbbr: ['日', '一', '二', '三', '四', '五', '六']
	}

	const en = {
		weekdaysFull: ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
		weekdaysShort: ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
		weekdaysAbbr: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
	}

	const zh_month = ['一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月']
	export default {
		props: {
			weekStartDay: {
				type: Number,
				default: 1 // 0:周日开始 1:周一开始 2:周二开始... 
			},
			headerMode: {
				type: Number,
				default: 2 //0:fullname 1:shortname 2:abbreviations
			},
			language: {
				type: Number,
				default: 0 // 0:zh 1:en
			},
		},
		data() {
			return {
				rowHeight: 80,
				weekcount: 6,
				weekdays: [],
				weekheader: [],

				swiperItemCount: 3,
				lastCurrent: 2,
				current: 0,

				currentMonth: dayjs().subtract(1, 'month').startOf('month').toDate(),
				swiperCalendar: [
					[],
					[],
					[]
				],

				selectedDate: new Date(),
				selectedRow: 0
			};
		},
		mounted() {
			this.weekdays = this.getWeekDays()
			this.weekheader = this.getWeekHeader()
			this.updateCalendar()
			console.log(this.swiperCalendar[this.current].length)
		},
		methods: {
			changeCalendar(event) {
				this.lastCurrent = this.current
				this.current = event.detail.current

				this.updateCalendar()
			},
			updateCalendar() {
				var current = this.current
				var curMonth = new Date()
				var prevMonth = new Date()
				var nextMonth = new Date()

				// left: month--
				if (this.lastCurrent - current == 1 || this.lastCurrent - current == -(this.swiperItemCount - 1)) {
					curMonth = dayjs(this.currentMonth).subtract(1, 'month').startOf('month').toDate()
					prevMonth = dayjs(curMonth).subtract(1, 'month').startOf('month').toDate()
					nextMonth = dayjs(curMonth).add(1, 'month').startOf('month').toDate()
				}
				// right: month++
				if (this.lastCurrent - current == -1 || this.lastCurrent - current == this.swiperItemCount - 1) {
					curMonth = dayjs(this.currentMonth).add(1, 'month').startOf('month').toDate()
					prevMonth = dayjs(curMonth).subtract(1, 'month').startOf('month').toDate()
					nextMonth = dayjs(curMonth).add(1, 'month').startOf('month').toDate()
				}

				var leftIndex = 0
				var rightIndex = 0

				if (current == 0) {
					leftIndex = this.swiperItemCount - 1
				} else {
					leftIndex = current - 1
				}

				if (current == this.swiperItemCount - 1) {
					rightIndex = 0
				} else {
					rightIndex = current + 1
				}

				this.currentMonth = curMonth
				this.$emit('monthChanged', this.currentMonth)
				console.log('month changed: ', `${curMonth.getFullYear()}-${curMonth.getMonth()+1}`)

				this.swiperCalendar[current] = this.getMonthCalendar(curMonth)
				this.swiperCalendar[leftIndex] = this.getMonthCalendar(prevMonth)
				this.swiperCalendar[rightIndex] = this.getMonthCalendar(nextMonth)
			},


			// 周开始时间
			getWeekDays() {
				if (this.weekStartDay == 0) {
					return WEEKDAYS
				}
				return DOUBLE_WEEKDAYS.slice(this.weekStartDay, this.weekStartDay + 7)
			},
			getWeekHeader() {
				var weekheader = []
				var lang = this.language == 0 ? zh : en
				for (var i = 0; i < this.weekdays.length; ++i) {
					var day = this.weekdays[i]
					if (this.headerMode == 0) {
						weekheader.push(lang.weekdaysFull[day])
					} else if (this.headerMode == 1) {
						weekheader.push(lang.weekdaysShort[day])
					} else {
						weekheader.push(lang.weekdaysAbbr[day])
					}
				}
				return weekheader
			},
			getZhMonth(date) {
				var da = new Date(date)
				// console.log(da.toLocaleDateString(), da.getMonth())
				return zh_month[da.getMonth()]
			},

			getMonthCalendar(date) {
				var selectYear = new Date(date).getFullYear()
				var selectMonth = new Date(date).getMonth() + 1

				var monthDayNum = new Date(selectYear, selectMonth, 0)
					.getDate(); //2022-11-0 得到的是11月份的总天数 (因为js中11已经是12月份，12月的0就是12月1日的前一天)

				const cursor = new Date(selectYear, selectMonth - 1, 1) //获取11月份
				const frontNum = this.weekdays.indexOf(cursor.getDay())
				cursor.setDate(cursor.getDate() - frontNum)

				var calendarDayNum = frontNum + monthDayNum
				this.weekcount = Math.ceil(calendarDayNum / 7)
				const backNum = 7 - calendarDayNum % 7
				calendarDayNum = calendarDayNum + backNum
				// console.log("calendarDayNum", calendarDayNum, "week count :", this.weekcount)

				let calendar = []
				var week = []
				for (var i = 0; i < calendarDayNum; i++) {
					if (i % 7 == 0) {
						if (week.length == 7) {
							calendar.push(week)
						}
						week = []
					}
					week.push(new Date(cursor))
					cursor.setDate(cursor.getDate() + 1)

					if (i == calendarDayNum - 1 && week.length > 0) {
						calendar.push(week)
					}
				}
				return calendar
			},
			getWeekCaledar(selectYear, selectMonth, selectWeek) {

			},
			getSwiperNextIndex(index) {
				if (index == -1) {
					if (this.current == 0) {
						return this.swiperItemCount - 1;
					} else {
						return this.current - 1
					}
				} else {
					if (this.current == this.swiperItemCount - 1) {
						return 0;
					} else {
						return this.current + 1
					}
				}
			},
			clickDate(date, row) {
				if (this.selectedDate == date) {
					return
				}

				if (date.getMonth() != this.currentMonth.getMonth()) {
					// this.lastCurrent = this.current
					// // 之前：跳到上个月
					// if (dayjs(date).isBefore(dayjs(this.currentMonth))) {
					// 	this.current = this.getSwiperNextIndex(-1);
					// }
					// // 之后：跳到下个月 
					// else {
					// 	this.current = this.getSwiperNextIndex(1);
					// }
					// // this.updateCalendar()
					return
				}
				this.selectedDate = date;
				this.selectedRow = row;
				this.$emit("dateChanged", date)
				console.log('date changed: ', new Date(date).toLocaleDateString())
			}
		}
	}
</script>

<style lang="scss">
	.no-select-day {
		background-color: transparent;
	}

	.select-day {
		background-color: lightpink;
		color: white;
		border-radius: 10px;

	}

	.calendar {
		width: 750rpx;
	}

	.weeks {
		display: flex;
		flex: 1;
		align-items: center;
		flex-wrap: nowrap;
		justify-content: space-around;
	}

	.row {
		display: flex;
		flex-direction: row;
		justify-content: space-around;
		align-items: center;
		flex-wrap: nowrap;
		margin-left: 10rpx;
		margin-right: 10rpx;
		margin-bottom: 10px;
		height: 60rpx;
	}

	.column {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.date {
		width: 20px;
		text-align: center;

		&.disabled {
			color: darkgray;
		}

		&.normal {
			color: black;
		}
	}

	.header {
		lex: 1;
		text-align: center;
		height: 80rpx;
		font-size: 26rpx;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		position: relative;
	}

	.zh-month-text {
		font-size: 6px;
	}

	.not-cur-month-day {
		color: dimgrey;
	}
</style>

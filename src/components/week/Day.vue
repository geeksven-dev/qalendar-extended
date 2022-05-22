<template>
	<div class="calendar-week__day">
		<DayEvent v-for="(event, eventIndex) in events"
				  :key="eventIndex"
				  :event-prop="event"
				  :day="day"
				  :time="time"
				  @event-was-clicked="$emit('event-was-clicked', $event)"
				  @event-was-resized="handleEventWasResized" />
	</div>
</template>

<script lang="ts">
import {defineComponent, PropType} from "vue";
import {dayInterface} from "../../typings/interfaces/day.interface";
import DayEvent from "./DayEvent.vue";
import EventConcurrency from "../../helpers/EventConcurrency";
import { eventInterface } from "../../typings/interfaces/event.interface";
import Time from "../../helpers/Time";
const eventConcurrencyHelper = new EventConcurrency()

export default defineComponent({
	name: 'Day',

	components: { DayEvent },

	props: {
		day: {
			type: Object as PropType<dayInterface>,
			required: true,
		},
		time: {
			type: Object as PropType<Time>,
			required: true,
		},
	},

	emits: ['event-was-clicked', 'event-was-resized'],

	data() {
		return {
			events: [] as eventInterface[],
		}
	},

	methods: {
		calculateEventConcurrency() {
			this.events = eventConcurrencyHelper.calculateConcurrencyForEvents(this.day.events)
		},
		
		handleEventWasResized(event: any) {
			this.$emit('event-was-resized', event)
			this.calculateEventConcurrency()
		}
	},

	mounted() {
		this.calculateEventConcurrency()
	}
})
</script>

<style scoped lang="scss">

.calendar-week__day {
	position: relative;
	width: 100%;
	height: 100%;
	overflow: hidden;

	&:first-child {
		border-left: 1px dashed rgb(224, 224, 224);
	}

	&:not(:last-child) {
		border-right: 1px dashed rgb(224, 224, 224);
	}
}
</style>
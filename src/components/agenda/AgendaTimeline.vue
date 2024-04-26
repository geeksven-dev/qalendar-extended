<template>
  <div class="agenda-view ps">
    <div v-for="datesCluster in clusterDates" v-bind:key="datesCluster.date" class="agenda-item">
      <div class="agenda-item__title">{{ formatDateDe(datesCluster.date) }}</div>
      <div class="event-flyout date-item" v-for="evt in datesCluster.dates" v-bind:key="evt.id">
        <div class="event-flyout__info-wrapper">

          <div class="agenda-item__row is-title">
            <div class="agenda-item__icon" :style="{backgroundColor: EVENT_COLORS[evt.color]}"></div>
            Meeting: {{ evt.title }}
          </div>


        </div>
        <div class="event-flyout__row is-time">{{ formatTime(evt.time) }}</div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import {defineComponent, PropType} from 'vue';
import Time from "../../helpers/Time";
import {eventInterface} from "../../typings/interfaces/event.interface";
import moment from "moment";
import {EVENT_COLORS} from "../../constants";

export default defineComponent({
  name: 'AgendaTimeline',

  props: {
    eventsProp: {
      type: Array as PropType<eventInterface[]>
    },
  },

  computed: {
    EVENT_COLORS() {
      return EVENT_COLORS
    },
    clusterDates: function () {
      // this gives an object with dates as keys
      if (this.eventsProp) {
        const groups: {
          [date: string]: eventInterface[]
        } = this.eventsProp.filter(evt => moment(evt.time.start).isAfter(moment()) && moment(evt.time.start).isBefore(moment().add(7, 'days'))).reduce((groups: {
          [date: string]: eventInterface[]
        }, event: eventInterface) => {
          const date: string = event.time.start.split(' ')[0];
          if (!groups[date]) {
            groups[date] = [];
          }
          groups[date].push(event);
          return groups;
        }, {});

// Edit: to add it in the array format instead
        const groupArrays = Object.keys(groups).map((date) => {
          return {
            date,
            dates: groups[date]
          };
        });

        console.log(groupArrays);
        return groupArrays;
      }
    }
  },

  data() {
    return {
      events: this.eventsProp || [],
    }
  },

  methods: {
    formatTime: function (eventTime: any) {
      return eventTime.start.split(' ')[1] + " - " + eventTime.end.split(' ')[1];
    },
    formatDateDe: function (date: string) {
      return moment(date).format('DD.MM.YYYY');
    }
  },

  watch: {
    // whenever question changes, this function will run
    events: function (newEvents, _) {
      console.log("new events:", newEvents)
    }
  }
});
</script>

<style scoped lang="scss">
@use '../../styles/mixins' as mixins;

.calendar-root-wrapper {
  height: auto !important;
}

.ps {
  overflow: scroll !important;
  overflow-anchor: none;
  -ms-overflow-style: none;
  touch-action: auto;
  -ms-touch-action: auto;
  overflow-y: auto !important;
}

.agenda-view {
  padding: 10px;
}

.agenda-item {
  border: var(--qalendar-border-gray-thin);
  border-radius: 8px;
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.09), 0 6px 12px rgba(0, 0, 0, 0.18);
  margin: 10px 10px;
  padding: 0px 10px;

  &__title {
    margin-bottom: 10px;
    margin-top: 10px;
    font-weight: bold;
  }

  &__row {
    display: flex;
    grid-gap: var(--qalendar-spacing);
    margin-bottom: 0.25em;
    font-weight: 400;
  }

  &__icon {
    --icon-height: 16px;
    border-radius: 50%;
    height: var(--icon-height);
    width: var(--icon-height);
  }

  .is-title {
    font-size: var(--qalendar-font-l);
    align-items: center;

    .is-not-editable & {
      max-width: 90%;
    }
  }

}

.date-item {
  margin-bottom: 10px;
}

</style>

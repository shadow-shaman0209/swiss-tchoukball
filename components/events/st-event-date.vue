<template>
  <div class="c-event-date" :class="{ 'c-event-date--one-line': alwaysOneLine }">
    <span class="c-event-date__week-days">{{ weekDays }}</span>
    <span class="c-event-date__days">{{ days }}</span>
    <span class="c-event-date__months">{{ months }}</span>
  </div>
</template>

<script lang="ts">
import Vue, { PropType } from 'vue';
import { isSameDay, isSameMonth } from 'date-fns';

export default Vue.extend({
  props: {
    startDate: {
      type: Date as PropType<Date>,
      required: true,
    },
    endDate: {
      type: Date as PropType<Date>,
      default: null,
    },
    /**
     * Renders the date on a single line regardless of the viewport,
     * as opposed to the default behaviour which wraps the date on three lines
     * starting from viewport small and up.
     */
    alwaysOneLine: Boolean,
  },
  computed: {
    isSingleDay(): boolean {
      return !this.endDate || isSameDay(this.startDate, this.endDate);
    },
    isWithinSingleMonth(): boolean {
      return !this.endDate || isSameMonth(this.startDate, this.endDate);
    },
    weekDays(): string {
      let weekDays = this.$formatDate(this.startDate, 'EEEE');
      if (!this.isSingleDay) {
        weekDays = `${this.$formatDate(this.startDate, 'EEE')} - ${this.$formatDate(this.endDate, 'EEE')}`;
        weekDays = weekDays.replace(/\./g, '');
      }

      return weekDays;
    },
    days(): string {
      let days = this.$formatDate(this.startDate, 'd');
      if (!this.isSingleDay) {
        days += ` - ${this.$formatDate(this.endDate, 'd')}`;
      }

      return days;
    },
    months(): string {
      let months = this.$formatDate(this.startDate, 'MMMM');
      if (!this.isWithinSingleMonth) {
        months = `${this.$formatDate(this.startDate, 'MMM')} - ${this.$formatDate(this.endDate, 'MMM')}`;
        months = months.replace(/\./g, '');
      }

      return months;
    },
  },
});
</script>

<style scoped>
.c-event-date {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  font-weight: 900;
  text-transform: uppercase;
  white-space: nowrap;
}

.c-event-date > * {
  margin-right: var(--st-length-spacing-xxs);
}

.c-event-date__week-days {
  color: var(--st-color-event-day-of-week);
}

.c-event-date__months {
  color: var(--st-color-event-month);
}

@media (--sm-and-up) {
  .c-event-date:not(.c-event-date--one-line) {
    flex-shrink: 0;
    flex-direction: column;
    margin-right: var(--st-length-spacing-s);
    width: 7rem;
  }

  .c-event-date:not(.c-event-date--one-line) > * {
    margin-right: 0;
  }

  .c-event-date:not(.c-event-date--one-line) .c-event-date__days {
    font-size: 2em;
  }
}
</style>
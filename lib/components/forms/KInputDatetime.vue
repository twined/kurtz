<template>
  <div :class="{'form-group': true, 'has-danger': hasError }">
    <div class="label-wrapper">
      <label
        :for="id"
        class="control-label">
        {{ label }}
      </label>
      <span>
        <i class="fa fa-exclamation-circle text-danger" />
        {{ errorText }}
      </span>
    </div>

    <VueFlatpickr
      :id="id"
      v-model="innerValue"
      :placeholder="placeholder"
      :options="dateTimeOptions"
      class="form-control" />
  </div>
</template>

<script>
import moment from 'moment-timezone'
import VueFlatpickr from '@jacobmischka/vue-flatpickr'

const LOCALE_NO = {
  weekdays: {
    shorthand: ['Søn', 'Man', 'Tir', 'Ons', 'Tor', 'Fre', 'Lør'],
    longhand: [
      'Søndag',
      'Mandag',
      'Tirsdag',
      'Onsdag',
      'Torsdag',
      'Fredag',
      'Lørdag'
    ]
  },
  months: {
    shorthand: [
      'Jan',
      'Feb',
      'Mar',
      'Apr',
      'Mai',
      'Jun',
      'Jul',
      'Aug',
      'Sep',
      'Okt',
      'Nov',
      'Des'
    ],
    longhand: [
      'Januar',
      'Februar',
      'Mars',
      'April',
      'Mai',
      'Juni',
      'Juli',
      'August',
      'September',
      'Oktober',
      'November',
      'Desember'
    ]
  },
  firstDayOfWeek: 1,
  rangeSeparator: ' til ',
  weekAbbreviation: 'Uke',
  scrollTitle: 'Scroll for å endre',
  toggleTitle: 'Klikk for å veksle',
  ordinal: function () {
    return '.'
  }
}

export default {
  components: {
    VueFlatpickr
  },

  props: {
    dateTimeOptions: {
      type: Object,
      default: () => {
        return {
          enableTime: true,
          minuteIncrement: 15,
          time_24hr: true,
          locale: LOCALE_NO,
          altInput: true,
          altFormat: 'l j F, Y @ H:i',
          dateFormat: 'Z'
        }
      }
    },

    hasError: {
      type: Boolean,
      default: false
    },

    errorText: {
      type: String,
      default: ''
    },

    label: {
      type: String,
      required: true
    },

    placeholder: {
      type: String,
      required: false,
      default: ''
    },

    name: {
      type: String,
      required: true
    },

    value: {
      type: String,
      default: () => {
        return moment.tz('Europe/Oslo').format()
      }
    }
  },

  data () {
    return {
      innerValue: ''
    }
  },

  computed: {
    id () {
      return this.name.replace('[', '_').replace(']', '_')
    }
  },

  watch: {
    innerValue (value) {
      this.$emit('input', value)
    },

    value (value) {
      this.innerValue = value
    }
  },

  created () {
    this.innerValue = this.value
  }
}
</script>

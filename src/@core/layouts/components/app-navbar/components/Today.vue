<template>
  <div>
    <feather-icon
        icon="CalendarIcon"
        size="21"
    />
    {{getDate()}}
  </div>
</template>
<script>
import moment from 'jalali-moment'
export default {
  name: 'Today',
  components: {
    moment
  },
  methods: {
    getDate(){
      let date=new Date()
      return moment(date, 'YYYY-M-D').locale('fa').format('dddd D MMM YYYY')
    }
  },
}
</script>

<template>
  <b-row>
    <b-col cols="12">
      <form-create-profile/>
      <form-wizard-number />
      <form-wizard-vertical />
      <form-wizard-icon />
      <form-wizard-vertical-icon />
    </b-col>
  </b-row>
</template>

<script>
import { BRow, BCol } from 'bootstrap-vue'
import FormWizardNumber from './FormWizardNumber.vue'
import FormWizardIcon from './FormWizardIcon.vue'
import FormWizardVertical from './FormWizardVertical.vue'
import FormWizardVerticalIcon from './FormWizardVerticalIcon.vue'
import FormCreateProfile from '@/views/forms/form-wizard/FormCreateProfile'

export default {
  components: {
    FormCreateProfile,
    BRow,
    BCol,
    FormWizardNumber,
    FormWizardIcon,
    FormWizardVertical,
    FormWizardVerticalIcon,
  },
}
</script>

<style lang="scss">
  @import '@core/scss/vue/libs/vue-wizard.scss';
  @import '@core/scss/vue/libs/vue-select.scss';
</style>

<template>
  <b-card
      no-body
      class="card-browser-states"
  >
    <b-card-header>
      <div>
        <b-card-title>
          {{ title }}
        </b-card-title>
      </div>
      <feather-icon
          icon="PlusIcon"
          size="18"
          class="cursor-pointer"
      />
    </b-card-header>

    <b-card-body>
<!--      <validation-observer ref="simpleRules">-->
<!--        <b-form>-->
<!--          <b-row>-->
<!--            <b-col md="2">-->
<!--              <b-form-group>-->
<!--                <validation-provider-->
<!--                    #default="{ errors }"-->
<!--                    name="fromDate"-->
<!--                    rules=""-->
<!--                >-->
<!--                  <date-picker label="از تاریخ" v-model="fromDate" :auto-submit="true"/>-->
<!--                  <small class="text-danger">{{ errors[0] }}</small>-->
<!--                </validation-provider>-->
<!--              </b-form-group>-->
<!--            </b-col>-->
<!--            <b-col md="2">-->
<!--              <b-form-group>-->
<!--                <validation-provider-->
<!--                    #default="{ errors }"-->
<!--                    name="toDate"-->
<!--                    rules=""-->
<!--                >-->
<!--                  <date-picker style="border-radius: 0px" label="تا تاریخ" v-model="toDate" :auto-submit="true"/>-->
<!--                  <small class="text-danger">{{ errors[0] }}</small>-->
<!--                </validation-provider>-->
<!--              </b-form-group>-->
<!--            </b-col>-->
<!--            <b-col cols="2">-->
<!--              <b-button-->
<!--                  variant="primary"-->
<!--                  type="submit"-->
<!--                  @click.prevent="validationForm"-->
<!--              >-->
<!--                جستوجو-->
<!--              </b-button>-->
<!--            </b-col>-->
<!--          </b-row>-->
<!--        </b-form>-->
<!--      </validation-observer>-->
      <div v-if="loading" style="text-align: center;color: #30007e;">
        لطفا چند لحظه صبر کنید
        <b-spinner variant="primary" label="Spinning"></b-spinner>
      </div>
      <b-table
          striped
          hover
          responsive
          class="position-relative"
          :per-page="perPage"
          :current-page="currentPage"
          :items="items"
          :fields="fields"
          :sort-by.sync="sortBy"
          :sort-desc.sync="sortDesc"
          :sort-direction="sortDirection"
          :filter="filter"
          :filter-included-fields="filterOn"
          @filtered="onFiltered"
          ref="table"
      >
        <template #cell(id)="data">
          {{ data.index + 1 }}
        </template>
        <template #cell(date)="data">
          {{ toShamsiDate(data.value) }}
        </template>
        <template #cell(amount)="data">
          <b-link :href="`/transaction/stuff-confirm/`+data.item.id">{{data.value}}</b-link>
        </template>
      </b-table>

      <b-card-body v-if="!loading" class="d-flex justify-content-between flex-wrap pt-0">

        <!-- page length -->
        <b-form-group
            label="درصفحه"
            label-cols="3"
            label-align="right"
            label-size="sm"
            label-for="sortBySelect"
            class="text-nowrap mb-md-0 mr-1"
        >
          <b-form-select
              id="perPageSelect"
              v-model="perPage"
              size="sm"
              inline
              :options="pageOptions"
          />
        </b-form-group>

        <!-- pagination -->
        <div>
          <b-pagination
              align="left"
              v-model="currentPage"
              :total-rows="totalRows"
              :per-page="perPage"
              first-number
              last-number
              prev-class="prev-item"
              next-class="next-item"
              class="mb-0"
          >
            <template #prev-text>
              <feather-icon
                  icon="ChevronLeftIcon"
                  size="18"
              />
            </template>
            <template #next-text>
              <feather-icon
                  icon="ChevronRightIcon"
                  size="18"
              />
            </template>
          </b-pagination>
        </div>
      </b-card-body>

      <template #code>
        {{ codeKitchenSink }}
      </template>
    </b-card-body>
  </b-card>
</template>

<script>
import BCardCode from '@core/components/b-card-code/BCardCode.vue'
import moment from 'jalali-moment'
import VuePersianDatetimePicker from 'vue-persian-datetime-picker'
import {TRANSACTION_SEARCH} from '@/constants/graphql'
import Ripple from 'vue-ripple-directive'
import FormCreateTransaction from "@/views/forms/form-validation/FormCreateTransaction";

import {
  BTable,
  BAvatar,
  BBadge,
  BFormGroup,
  BFormSelect,
  BPagination,
  BInputGroup,
  BFormInput,
  BInputGroupAppend,
  BButton,
  BCardBody,
  BLink,
  BCol,
  BRow,
  BForm,
  BCardText,
  BSpinner,
  BCard,
  BCardHeader,
  BCardTitle,
  BModal,
  VBModal,
} from 'bootstrap-vue'
import {codeKitchenSink} from './code'
import vSelect from 'vue-select'
import {ValidationProvider, ValidationObserver} from 'vee-validate'
import {GET_ALL_PRESENTER} from '@/constants/graphql'
import {required, email} from '@validations'

export default {
  components: {
    FormCreateTransaction,
    ValidationProvider,
    ValidationObserver,
    BCardCode,
    BTable,
    BAvatar,
    BBadge,
    BFormGroup,
    BFormSelect,
    BPagination,
    BInputGroup,
    BFormInput,
    BInputGroupAppend,
    BButton,
    BCardBody,
    BLink,
    vSelect,
    BCol,
    BRow,
    BForm,
    BCardText,
    BSpinner,
    BCard,
    BCardHeader,
    BCardTitle,
    BModal,
    datePicker: VuePersianDatetimePicker
  },
  directives: {
    'b-modal': VBModal,
    Ripple,
  },
  props: [
    'items',
    'title'
  ],
  data() {
    return {
      loading: 1,
      fromDate: '',
      toDate: '',
      mutableItems: this.items,
      selected: {title: null, value: null},
      kindSelected: {title: null, value: null},
      perPage: 30,
      pageOptions: [30, 50, 100],
      totalRows: 1,
      currentPage: 1,
      sortBy: '',
      sortDesc: false,
      sortDirection: 'asc',
      filter: null,
      filterOn: [],
      presenter: Array(),
      required,
      email,
      infoModal: {
        id: 'info-modal',
        title: 'sghl',
        content: '',
      },
      kind: [
        {title: "سپرده گزاری", value: "1"},
        {title: "برداشت", value: "2"},
        {title: "واریز سود", value: "3"},
        {title: "برداشت سود", value: "4"},
        {title: "واریز سود معرف", value: "5"}
      ],
      fields: [
        {key: 'id', label: '#', sortable: true},
        {key: 'amount', label: 'مبلغ', sortable: true},
        {key: 'date', label: 'تاریخ', sortable: true},
      ],
      kindIdType: [{
        1: 'سپرده گزاری', 2: 'برداشت', 3: 'واریز سود', 4: 'برداشت سود', 5: 'واریز سود معرفی',
      },
        {
          1: 'light-primary', 2: 'light-danger', 3: 'light-info', 4: 'light-warning', 5: 'light-success',
        }],
      codeKitchenSink,
    }
  },
  updated() {
    this.totalRows = this.items.length
  },
  computed: {
    sortOptions() {
      this.totalRows = this.items.length
      // Create an options list from our fields
      return this.fields
          .filter(f => f.sortable)
          .map(f => ({text: f.label, value: f.key}))
    },
  },
  mounted() {
    this.loading = 1;
    this.$apollo.mutate({
      mutation: GET_ALL_PRESENTER
    }).then((presenter) => {
      let presenters = new Array()
      let items = presenter.data.profiles.edges
      for (const item of items) {
        presenters.push({
          'title': item.node.firstName + ' ' + item.node.lastName,
          'value': item.node.id
        })
      }
      this.presenter = presenters
      this.loading = 0
    })
    // Set the initial number of items
    this.totalRows = this.items.length
  },
  watch: {
    items(val, oldVal) {
      // console.log(val,oldVal)
    }
  },
  methods: {
    validationForm() {
      this.$refs.simpleRules.validate().then(success => {
        if (success) {
          this.loading = 1;
          let input = {
            profile_Id: (this.selected ? this.selected.value : null),
            kind_Id: (this.kindSelected ? this.kindSelected.value : null),
            effectiveDate_Gte: this.fromDate,
            effectiveDate_Lte: this.toDate
          }
          if (this.fromDate === "") {
            delete input.effectiveDate_Gte
          }
          if (this.toDate === "") {
            delete input.effectiveDate_Lte
          }

          this.$apollo.mutate({
            mutation: TRANSACTION_SEARCH,
            variables: {
              effectiveDate_Gte: this.toGregorianDate(input.effectiveDate_Gte),
              effectiveDate_Lte: this.toGregorianDate(input.effectiveDate_Lte)
            }
          }).then((result) => {
            let transactions = new Array()
            let items = result.data.transactions.edges
            for (const item of items) {
              transactions.push({
                "id": item.node.id,
                "amount": item.node.amount,
                "date": item.node.effectiveDate
              })
            }
            this.totalRows = this.items.length
            this.items = transactions;
            this.$refs.table.refresh();
            this.loading = 0
          }).catch((result) => {
            alert("catch")
            JSON.stringify(this.fromDate);

          })
        }
      })
    },

    toShamsiDate(date) {
      return moment(date, 'YYYY-M-D').locale('fa').format('YYYY/MM/DD')
    },
    toGregorianDate(date) {
      if (date) {
        return moment.from(date, 'fa', 'YYYY/MM/DD').locale('en').format('YYYY-MM-DD')
      }
    },
    convertToCurrency(Number) {
      var x, y, z;
      Number += '';
      Number = Number.replace(',', '');
      Number = Number.replace(',', '');
      Number = Number.replace(',', '');
      Number = Number.replace(',', '');
      Number = Number.replace(',', '');
      Number = Number.replace(',', '');
      x = Number.split('.');
      y = x[0];
      z = x.length > 1 ? '.' + x[1] : '';
      var rgx = /(\d+)(\d{3})/;
      while (rgx.test(y))
        y = y.replace(rgx, '$1' + ',' + '$2');
      return y + z;
    },
    info(item, index, button) {
      this.infoModal.title = `Row index: ${index}`
      this.infoModal.content = JSON.stringify(item, null, 2)
      this.$root.$emit('bv::show::modal', this.infoModal.id, button)
    },
    resetInfoModal() {
      this.infoModal.title = ''
      this.infoModal.content = ''
    },
    onFiltered(filteredItems) {
      // Trigger pagination to update the number of buttons/pages due to filtering
      this.totalRows = filteredItems.length
      this.currentPage = 1
    },
  },
}
</script>

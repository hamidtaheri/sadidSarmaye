<template>
  <b-card-code
      :title="title"
  >
    <b-card-text>
      <span>ویرایش مشخصات کاربر</span>
    </b-card-text>

    <!-- form -->
    <validation-observer ref="simpleRules">
      <b-form>
        <b-row>
          <b-col md="6">
            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="First Name"
                  rules="required"
              >
                <b-form-input
                    v-model="firstName"
                    :state="errors.length > 0 ? false:null"
                    placeholder="نام"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ********************* -->

            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="Last Name"
                  rules="required"
              >
                <b-form-input
                    v-model="lastName"
                    :state="errors.length > 0 ? false:null"
                    placeholder="نام خانوادگی"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ********************* -->

            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="codeMEli"
                  rules="required"
              >
                <b-form-input
                    v-model="codeMeli"
                    :state="errors.length > 0 ? false:null"
                    placeholder="کدملی"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ********************* -->

            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="adress"
                  rules="required"
              >
                <v-select
                    v-model="selected"
                    :dir="rtl"
                    label="title"
                    :options="presenter"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="adress"
                  rules="required"
              >
                <b-form-input
                    v-model="adress"
                    :state="errors.length > 0 ? false:null"
                    placeholder="آدرس"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ********************* -->

            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="shomareKart"
                  rules="required"
              >
                <b-form-input
                    v-model="shomareKart"
                    :state="errors.length > 0 ? false:null"
                    placeholder="شماره کارت"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ********************* -->

            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="shomareHesab"
                  rules="required"
              >
                <b-form-input
                    v-model="shomareHesab"
                    :state="errors.length > 0 ? false:null"
                    placeholder="شماره حساب"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ********************* -->

            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="description"
                  rules="required"
              >
                <b-form-input
                    v-model="description"
                    :state="errors.length > 0 ? false:null"
                    placeholder="توضیحات"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ********************* -->

            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="tel"
                  rules="required"
              >
                <b-form-input
                    v-model="tel"
                    :state="errors.length > 0 ? false:null"
                    placeholder="تلفن"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ********************* -->

            <!-- ******************** -->
            <b-form-group>
              <validation-provider
                  #default="{ errors }"
                  name="mobile1"
                  rules="required"
              >
                <b-form-input
                    v-model="mobile1"
                    :state="errors.length > 0 ? false:null"
                    placeholder="موبایل"
                />
                <small class="text-danger">{{ errors[0] }}</small>
              </validation-provider>
            </b-form-group>
            <!-- ********************* -->
          </b-col>
          <b-col cols="12">
            <b-button
                variant="primary"
                type="submit"
                @click.prevent="validationForm"
            >
              ذخیره
            </b-button>
          </b-col>
        </b-row>
      </b-form>
    </validation-observer>

    <template #code>
      {{ codeSimple }}
    </template>
  </b-card-code>
</template>

<script>
import BCardCode from '@core/components/b-card-code'
import {ValidationProvider, ValidationObserver} from 'vee-validate'
import vSelect from 'vue-select'
import {
  BFormInput, BFormGroup, BForm, BRow, BCol, BButton, BCardText,
} from 'bootstrap-vue'
import {required, email} from '@validations'
import {UPDATE_PROFILE, PROFILE, GET_ALL_PRESENTER,} from '@/constants/graphql'


export default {
  props: [
    'title',
    'user'
  ],
  mounted() {
    this.$apollo.mutate({
      mutation: PROFILE,
      variables: {
        id: this.$route.params.id
      }
    }).then((result) => {
      this.$apollo.mutate({
        mutation: GET_ALL_PRESENTER
      }).then((presenter) => {
        var presenters = new Array()
        var items = presenter.data.profiles.edges
        for (const item of items) {
          presenters.push({
            'title': item.node.firstName + ' ' + item.node.lastName,
            'value': item.node.id
          })
        }
        this.presenter = presenters
      })
      this.id = result.data.profiles.edges[0].node.id;
      this.firstName = result.data.profiles.edges[0].node.firstName;
      this.lastName = result.data.profiles.edges[0].node.lastName;
      this.codeMeli = result.data.profiles.edges[0].node.codeMeli;
      this.presenter = result.data.profiles.edges[0].node.presenter;
      this.selected = result.data.profiles.edges[0].node.presenter;
      this.adress = result.data.profiles.edges[0].node.adress;
      this.shomareKart = result.data.profiles.edges[0].node.shomareKart;
      this.shomareHesab = result.data.profiles.edges[0].node.shomareHesab;
      this.description = result.data.profiles.edges[0].node.description;
      this.tel = result.data.profiles.edges[0].node.tel;
      this.mobile1 = result.data.profiles.edges[0].node.mobile1;
    }).catch((result) => {
      alert("catch")
    })
  },
  components: {
    BCardCode,
    ValidationProvider,
    ValidationObserver,
    BCardText,
    BFormInput,
    BFormGroup,
    BForm,
    BRow,
    BCol,
    BButton,
    vSelect
  },
  data() {
    return {
      selected: {title: ''},
      id: '',
      firstName: '',
      lastName: '',
      codeMeli: '',
      presenter: '',
      adress: '',
      shomareKart: '',
      shomareHesab: '',
      description: '',
      tel: '',
      mobile1: '',
      required,
      email,
    }
  },
  methods: {
    validationForm() {
      this.$refs.simpleRules.validate().then(success => {
        if (success) {
          // eslint-disable-next-line
          var input = {
            id: Number(this.id),
            firstName: this.firstName,
            lastName: this.lastName,
            codeMeli: this.codeMeli,
            presenterId: this.selected.value,
            adress: this.adress,
            shomareKart: this.shomareKart,
            shomareHesab: this.shomareHesab,
            description: this.description,
            tel: this.tel,
            mobile1: this.mobile1
          }
          if (!this.selected.value) {
            delete input.presenterId

          }
          this.$apollo.mutate({
            mutation: UPDATE_PROFILE,
            variables: {
              input: input
            }
          }).then((result) => {
            alert("ok")
            console.log(result)
          }).catch((result) => {
            alert("catch")
          })
        }
      })
    },
  },
}
</script>

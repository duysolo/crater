<template>
  <div class="customer-create main-content">
    <form action="" @submit.prevent="submitCustomerData">
      <div class="page-header">
        <h3 class="page-title">{{ isEdit ? $t('customers.edit_customer') : $t('customers.new_customer') }}</h3>
        <ol class="breadcrumb">
          <li class="breadcrumb-item"><router-link slot="item-title" to="/admin/dashboard">{{ $t('general.home') }}</router-link></li>
          <li class="breadcrumb-item"><router-link slot="item-title" to="/admin/customers">{{ $tc('customers.customer', 2) }}</router-link></li>
          <li class="breadcrumb-item">{{ isEdit ? $t('customers.edit_customer') : $t('customers.new_customer') }}</li>
        </ol>
        <div class="page-actions header-button-container">
          <base-button
            :loading="isLoading"
            :disabled="isLoading"
            :tabindex="23"
            icon="save"
            color="theme"
            type="submit"
          >
            {{ isEdit ? $t('customers.update_customer') : $t('customers.save_customer') }}
          </base-button>
        </div>
      </div>
      <div class="customer-card card">
        <div class="card-body">
          <div class="row">
            <div class="section-title col-sm-2">{{ $t('customers.basic_info') }}</div>
            <div class="col-sm-5">
              <div class="form-group">
                <label class="form-label">{{ $t('customers.display_name') }}</label><span class="text-danger"> *</span>
                <base-input
                  :invalid="$v.formData.name.$error"
                  v-model="formData.name"
                  focus
                  type="text"
                  name="name"
                  tab-index="1"
                  @input="$v.formData.name.$touch()"
                />
                <div v-if="$v.formData.name.$error">
                  <span v-if="!$v.formData.name.required" class="text-danger">{{ $tc('validation.required') }}</span>
                  <span v-if="!$v.formData.name.minLength" class="text-danger"> {{ $tc('validation.name_min_length', $v.formData.name.$params.minLength.min, { count: $v.formData.name.$params.minLength.min }) }} </span>
                </div>
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.email') }}</label>
                <base-input
                  :invalid="$v.formData.email.$error"
                  v-model.trim="formData.email"
                  type="text"
                  name="email"
                  tab-index="3"
                  @input="$v.formData.email.$touch()"
                />
                <div v-if="$v.formData.email.$error">
                  <span v-if="!$v.formData.email.email" class="text-danger"> {{ $tc('validation.email_incorrect') }} </span>
                </div>
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.primary_currency') }}</label>
                <base-select
                  v-model="currency"
                  :options="currencies"
                  :allow-empty="false"
                  :searchable="true"
                  :show-labels="false"
                  :tabindex="5"
                  :placeholder="$t('customers.select_currency')"
                  label="name"
                  track-by="id"
                />
              </div>
            </div>
            <div class="col-sm-5">
              <div class="form-group">
                <label class="form-label">{{ $t('customers.primary_contact_name') }}</label>
                <base-input
                  v-model.trim="formData.contact_name"
                  :label="$t('customers.contact_name')"
                  type="text"
                  tab-index="2"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.phone') }}</label>
                <base-input
                  v-model.trim="formData.phone"
                  type="text"
                  name="phone"
                  tab-index="4"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.website') }}</label>
                <base-input
                  v-model="formData.website"
                  :invalid="$v.formData.website.$error"
                  type="url"
                  @input="$v.formData.website.$touch()"
                />
                <div v-if="$v.formData.website.$error">
                  <span v-if="!$v.formData.website.url" class="text-danger">{{ $tc('validation.invalid_url') }}</span>
                </div>
              </div>
            </div>
          </div>
          <hr> <!-- first row complete  -->
          <div class="row">
            <div class="section-title col-sm-2">{{ $t('customers.billing_address') }}</div>
            <div class="col-sm-5">
              <div class="form-group">
                <label class="form-label">{{ $t('customers.name') }}</label>
                <base-input
                  v-model.trim="billing.name"
                  type="text"
                  name="address_name"
                  tab-index="7"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.state') }}</label>
                <base-select
                  v-model="billing_state"
                  :options="billingStates"
                  :searchable="true"
                  :show-labels="false"
                  :tabindex="9"
                  :disabled="isDisabledBillingState"
                  :placeholder="$t('general.select_state')"
                  label="name"
                  track-by="id"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.address') }}</label>
                <base-text-area
                  v-model.trim="billing.address_street_1"
                  :tabindex="11"
                  :placeholder="$t('general.street_1')"
                  type="text"
                  name="billing_street1"
                  rows="2"
                  @input="$v.billing.address_street_1.$touch()"
                />
                <div v-if="$v.billing.address_street_1.$error">
                  <span v-if="!$v.billing.address_street_1.maxLength" class="text-danger">{{ $t('validation.address_maxlength') }}</span>
                </div>
                <base-text-area
                  :tabindex="12"
                  v-model.trim="billing.address_street_2"
                  :placeholder="$t('general.street_2')"
                  type="text"
                  name="billing_street2"
                  rows="2"
                  @input="$v.billing.address_street_2.$touch()"
                />
                <div v-if="$v.billing.address_street_2.$error">
                  <span v-if="!$v.billing.address_street_2.maxLength" class="text-danger">{{ $t('validation.address_maxlength') }}</span>
                </div>
              </div>
            </div>
            <div class="col-sm-5">
              <div class="form-group">
                <label class="form-label">{{ $t('customers.country') }}</label>
                <base-select
                  v-model="billing_country"
                  :options="billingCountries"
                  :searchable="true"
                  :show-labels="false"
                  :allow-empty="false"
                  :tabindex="8"
                  :placeholder="$t('general.select_country')"
                  label="name"
                  track-by="id"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.city') }}</label>
                <base-select
                  v-model="billing_city"
                  :options="billingCities"
                  :searchable="true"
                  :show-labels="false"
                  :disabled="isDisabledBillingCity"
                  :tabindex="10"
                  :placeholder="$t('general.select_city')"
                  label="name"
                  track-by="id"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.phone') }}</label>
                <base-input
                  v-model.trim="billing.phone"
                  type="text"
                  name="phone"
                  tab-index="13"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.zip_code') }}</label>
                <base-input
                  v-model.trim="billing.zip"
                  type="text"
                  name="zip"
                  tab-index="14"
                />
              </div>
            </div>
          </div>
          <hr> <!-- second row complete  -->
          <div class="same-address-checkbox-container">
            <div class="p-1">
              <base-button ref="sameAddress" icon="copy" color="theme" class="btn-sm" @click="copyAddress(true)">
                {{ $t('customers.copy_billing_address') }}
              </base-button>
            </div>
          </div>

          <div class="row">
            <div class="section-title col-sm-2">
              {{ $t('customers.shipping_address') }}
            </div>
            <div class="col-sm-5">
              <div class="form-group">
                <label class="form-label">{{ $t('customers.name') }}</label>
                <base-input
                  v-model.trim="shipping.name"
                  type="text"
                  name="address_name"
                  tab-index="15"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.state') }}</label>
                <base-select
                  v-model="shipping_state"
                  :options="shippingStates"
                  :searchable="true"
                  :show-labels="false"
                  :tabindex="17"
                  :disabled="isDisabledShippingState"
                  :placeholder="$t('general.select_state')"
                  label="name"
                  track-by="id"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.address') }}</label>
                <base-text-area
                  v-model.trim="shipping.address_street_1"
                  :tabindex="19"
                  :placeholder="$t('general.street_1')"
                  type="text"
                  name="street_1"
                  rows="2"
                  @input="$v.shipping.address_street_1.$touch()"
                />
                <div v-if="$v.shipping.address_street_1.$error">
                  <span v-if="!$v.shipping.address_street_1.maxLength" class="text-danger">{{ $t('validation.address_maxlength') }}</span>
                </div>
                <base-text-area
                  v-model.trim="shipping.address_street_2"
                  :tabindex="20"
                  :placeholder="$t('general.street_2')"
                  type="text"
                  name="street_2"
                  rows="2"
                  @input="$v.shipping.address_street_2.$touch()"
                />
                <div v-if="$v.shipping.address_street_2.$error">
                  <span v-if="!$v.shipping.address_street_2.maxLength" class="text-danger">{{ $t('validation.address_maxlength') }}</span>
                </div>
              </div>
            </div>
            <div class="col-sm-5">
              <div class="form-group">
                <label class="form-label">{{ $t('customers.country') }}</label>
                <base-select
                  v-model="shipping_country"
                  :options="shippingCountries"
                  :searchable="true"
                  :show-labels="false"
                  :tabindex="16"
                  :allow-empty="false"
                  :placeholder="$t('general.select_country')"
                  label="name"
                  track-by="id"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.city') }}</label>
                <base-select
                  v-model="shipping_city"
                  :options="shippingCities"
                  :searchable="true"
                  :show-labels="false"
                  :tabindex="18"
                  :disabled="isDisabledShippingCity"
                  :placeholder="$t('general.select_city')"
                  label="name"
                  track-by="id"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.phone') }}</label>
                <base-input
                  v-model.trim="shipping.phone"
                  type="text"
                  name="phone"
                  tab-index="21"
                />
              </div>
              <div class="form-group">
                <label class="form-label">{{ $t('customers.zip_code') }}</label>
                <base-input
                  v-model.trim="shipping.zip"
                  type="text"
                  name="zip"
                  tab-index="22"
                />
              </div>
              <div class="form-group collapse-button-container">
                <base-button
                  :tabindex="23"
                  icon="save"
                  color="theme"
                  class="collapse-button"
                  type="submit"
                >
                  {{ $t('customers.save_customer') }}
                </base-button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
import MultiSelect from 'vue-multiselect'
import { validationMixin } from 'vuelidate'
import AddressStub from '../../stub/address'
const { required, minLength, email, numeric, url, maxLength } = require('vuelidate/lib/validators')

export default {
  components: { MultiSelect },
  mixins: [validationMixin],
  data () {
    return {
      isCopyFromBilling: false,
      isLoading: false,
      formData: {
        name: null,
        contact_name: null,
        email: null,
        phone: null,
        currency_id: null,
        website: null,
        addresses: []
      },
      currency: null,
      billing: {
        name: null,
        country_id: null,
        state_id: null,
        city_id: null,
        phone: null,
        zip: null,
        address_street_1: null,
        address_street_2: null,
        type: 'billing'
      },
      shipping: {
        name: null,
        country_id: null,
        state_id: null,
        city_id: null,
        phone: null,
        zip: null,
        address_street_1: null,
        address_street_2: null,
        type: 'shipping'
      },
      currencyList: [],
      isDisabledBillingState: true,
      isDisabledBillingCity: true,
      isDisabledShippingState: true,
      isDisabledShippingCity: true,

      billing_country: null,
      billing_city: null,
      billing_state: null,

      shipping_country: null,
      shipping_city: null,
      shipping_state: null,

      billingCountries: [],
      billingStates: [],
      billingCities: [],

      shippingCountries: [],
      shippingStates: [],
      shippingCities: []
    }
  },
  validations: {
    formData: {
      name: {
        required,
        minLength: minLength(3)
      },
      email: {
        email
      },
      website: {
        url
      }
    },
    billing: {
      address_street_1: {
        maxLength: maxLength(255)
      },
      address_street_2: {
        maxLength: maxLength(255)
      }
    },
    shipping: {
      address_street_1: {
        maxLength: maxLength(255)
      },
      address_street_2: {
        maxLength: maxLength(255)
      }
    }
  },
  computed: {
    ...mapGetters('currency', [
      'defaultCurrency',
      'currencies'
    ]),
    isEdit () {
      if (this.$route.name === 'customers.edit') {
        return true
      }
      return false
    }
  },
  watch: {
    billing_country (newCountry) {
      if (newCountry) {
        this.billing.country_id = newCountry.id
        this.isDisabledBillingState = false
        this.billing_state = null
        this.billing_city = null
        this.fetchBillingState()
      }
    },
    billing_state (newState) {
      if (newState) {
        this.billing.state_id = newState.id
        this.isDisabledBillingCity = false
        this.billing_city = null
        this.fetchBillingCities()
        return true
      }
      this.billing_city = null
      this.isDisabledBillingCity = true
      return true
    },
    billing_city (newCity) {
      if (newCity) {
        this.billing.city_id = newCity.id
      }
    },
    shipping_country (newCountry) {
      if (newCountry) {
        this.shipping.country_id = newCountry.id
        this.isDisabledShippingState = false
        this.fetchShippingState()
        if (this.isCopyFromBilling) {
          return true
        }
        this.shipping_state = null
        this.shipping_city = null
        return true
      }
    },
    shipping_state (newState) {
      if (newState) {
        this.shipping.state_id = newState.id
        this.isDisabledShippingCity = false
        this.fetchShippingCities()
        if (this.isCopyFromBilling) {
          this.isCopyFromBilling = false
          return true
        }
        this.shipping_city = null
        return true
      }
      this.shipping_city = null
      this.isDisabledShippingCity = true
      return true
    },
    shipping_city (newCity) {
      if (newCity) {
        this.shipping.city_id = newCity.id
      }
    }
  },
  mounted () {
    this.fetchCountry()
    if (this.isEdit) {
      this.loadCustomer()
    } else {
      this.currency = this.defaultCurrency
    }
  },
  methods: {
    ...mapActions('customer', [
      'addCustomer',
      'fetchCustomer',
      'updateCustomer'
    ]),
    async loadCustomer () {
      let { data: { customer, currencies, currency } } = await this.fetchCustomer(this.$route.params.id)
      this.formData = customer

      if (customer.billing_address) {
        this.billing = customer.billing_address

        if (customer.billing_address.country_id) {
          this.billing_country = this.billingCountries.find((c) => c.id === customer.billing_address.country_id)
        }
      }

      if (customer.shipping_address) {
        this.shipping = customer.shipping_address

        if (customer.shipping_address.country_id) {
          this.shipping_country = this.shippingCountries.find((c) => c.id === customer.shipping_address.country_id)
        }
      }

      this.currencyList = currencies
      this.formData.currency_id = customer.currency_id
      this.currency = currency
    },
    async fetchCountry () {
      let res = await window.axios.get('/api/countries')
      if (res) {
        this.billingCountries = res.data.countries
        this.shippingCountries = res.data.countries
      }
    },
    copyAddress (val) {
      if (val === true) {
        this.isCopyFromBilling = true
        this.shipping = {...this.billing, type: 'shipping'}
        this.shipping_country = this.billing_country
        this.shipping_state = this.billing_state
        this.shipping_city = this.billing_city
      } else {
        this.shipping = {...AddressStub, type: 'shipping'}
        this.shipping_country = null
        this.shipping_state = null
        this.shipping_city = null
      }
    },
    async submitCustomerData () {
      this.$v.formData.$touch()

      if (this.$v.$invalid) {
        return true
      }
      this.formData.addresses = [{...this.billing}, {...this.shipping}]

      if (this.isEdit) {
        if (this.currency) {
          this.formData.currency_id = this.currency.id
        }
        this.isLoading = true
        try {
          let response = await this.updateCustomer(this.formData)
          if (response.data) {
            window.toastr['success'](this.$t('customers.updated_message'))
            this.$router.push('/admin/customers')
            this.isLoading = false
            return true
          }
        } catch (err) {
          if (err.response.data.errors.email) {
            this.isLoading = false
            window.toastr['error'](this.$t('validation.email_already_taken'))
          }
        }
      } else {
        this.isLoading = true
        if (this.currency) {
          this.isLoading = true
          this.formData.currency_id = this.currency.id
        }
        try {
          let response = await this.addCustomer(this.formData)
          if (response.data.success) {
            window.toastr['success'](this.$t('customers.created_message'))
            this.$router.push('/admin/customers')
            this.isLoading = false
            return true
          }
        } catch (err) {
          if (err.response.data.errors.email) {
            this.isLoading = false
            window.toastr['error'](this.$t('validation.email_already_taken'))
          }
        }
      }
    },
    async fetchBillingState () {
      let res = await window.axios.get(`/api/states/${this.billing_country.id}`)
      if (res) {
        this.billingStates = res.data.states
      }
      if (this.isEdit) {
        this.billing_state = this.billingStates.find((state) => state.id === this.billing.state_id)
      }
    },
    async fetchBillingCities () {
      let res = await window.axios.get(`/api/cities/${this.billing_state.id}`)
      if (res) {
        this.billingCities = res.data.cities
      }
      if (this.isEdit) {
        this.billing_city = this.billingCities.find((city) => city.id === this.billing.city_id)
      }
    },
    async fetchShippingState () {
      let res = await window.axios.get(`/api/states/${this.shipping_country.id}`)
      if (res) {
        this.shippingStates = res.data.states
      }
      if (this.isEdit) {
        this.shipping_state = this.shippingStates.find((s) => s.id === this.shipping.state_id)
      }
    },
    async fetchShippingCities () {
      let res = await window.axios.get(`/api/cities/${this.shipping_state.id}`)
      if (res) {
        this.shippingCities = res.data.cities
      }
      if (this.isEdit) {
        this.shipping_city = this.shippingCities.find((c) => c.id === this.shipping.city_id)
      }
    }
  }
}
</script>

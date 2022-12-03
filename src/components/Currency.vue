<template>
  <div class="container">
    <div class="title-div row">
      <div class="col-2">
        <span class="title">Yet Another Forex</span>
      </div>
    </div>
    <br>
    <b-overlay :show="loading" spinner-type="grow" rounded="sm">
      <div class="row">
        <div class="col-6 d-flex justify-content-start">
          <h5>Rates as of {{ date }}</h5>
        </div>
        <div class="col-5 offset-1">
          <b-form-datepicker id="datepicker" v-model="date" class="mb-2"></b-form-datepicker>
        </div>
      </div>
      <div class="row">
        <div class="col-12">
          <b-row v-for="row in nrows" :key="row">
            <b-col v-for="col in 4" :key="col">
              <b-card
                v-if="row * 3 + col < keys.length"
                :header="keys[row*3+col]"
                :title="values[row*3+col].toString()"
                tag="article"
                style="max-width: 20rem;"
                class="mb-2"
              >
              </b-card>
            </b-col>
          </b-row>
        </div>
      </div>
    </b-overlay>
  </div>
</template>

<script>
import axios from 'axios';
import { API_URL, API_KEY } from '../../.env.js'

export default {
  name: 'Currency',
  props: {
    msg: String
  },

  data() {
    return {
      loading: false,
      date: 'Today',
      keys: [],
      values: [],
    }
  },

  computed: {
    nrows() {
      return Math.floor((this.keys.length - 1) / 4) + 1;
    }
  },

  methods: {
    async fetchLiveCurrencyData() {
      try {
        this.loading = true;

        const { data } = await axios.get(`${API_URL}/live`, {
          headers: {
            'apikey': API_KEY
          }
        });

        if (data) {
          this.keys = Object.keys(data.quotes);
          this.values = Object.values(data.quotes);
        }
      } catch (e) {
        alert(e);
      } finally {
        this.loading = false;
      }
    },

    async fetchHistoricalCurrencyData(params = {}) {
      try {
        this.loading = true;

        const { data } = await axios.get(`${API_URL}/historical?date=${params}`, {
          headers: {
            'apikey': API_KEY
          }
        });

        if (data) {
          this.keys = [];
          this.values = [];
          this.keys = Object.keys(data.quotes);
          this.values = Object.values(data.quotes);
        }
      } catch (e) {
        alert(e);
      } finally {
        this.loading = false;
      }
    }
  },

  watch: {
    date: function (value) {
      this.fetchHistoricalCurrencyData(value);
    }
  },

  mounted() {
    this.fetchLiveCurrencyData();
  },
}
</script>

<style>
  .title-div {
    background-color: #282A3A;
    padding: 0.5rem;
  }

  .title {
    color: #FFFFFF;
  }
</style>
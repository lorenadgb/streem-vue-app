<template>
  <div>
    <form v-on:submit.prevent="submitForm">
      <div class="form-group">
        <label for="query">Query</label>
        <input type="text" class="form-control" id="query" placeholder="Query" v-model="query">
      </div>

      <div class="form-group">
        <label for="after">Start Date</label>
        <date-picker v-model="after" id="after" type="date"></date-picker>
      </div>

      <div class="form-group">
        <label for="before">End Date</label>
        <date-picker v-model="before" id="before" type="date"></date-picker>
      </div>

      <div class="form-group">
        <label for="interval">Interval</label>
        <input type="range" name="interval" class="form-control-range" id="interval" min="1" max="31" v-model="interval">
        <span v-text="interval"></span>d
      </div>

      <div class="form-group">
        <button class="btn btn-primary">Submit</button>
      </div>

      <p class="text-red-600 text-xs italic mt-1" v-if="errorMessage">
        {{ errorMessage }}
      </p>
    </form>

    <StackedBar v-if="loaded"
      :label="labels"
      :chartData="chartData"
    />
  </div>
</template>

<script>
import axios from 'axios';
import DatePicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';
import moment from 'moment';
import StackedBar from "./graphs/StackedBar";

export default {
  name: 'PostFormAxios',
  components: {
    DatePicker,
    StackedBar
  },
  data(){
    return {
      loaded: false,
      labels: [],
      chartData: [],
      query: 'scott',
      before: new Date('2019-08-30'),
      after:  new Date('2019-08-01'),
      interval: 1,
      colours: {
        'TV':     '#f87979',
        'PRINT':  '#3D5B96',
        'ONLINE': '#1EFFFF',
        'RADIO':  '#3b662c',
        'SOCIAL': '#adab39'
      }
    }
  },
  computed: {
    errorMessage() {
      if (!this.query) return 'Query is required.';
      if (!this.before) return 'Start Date is required.';
      if (!this.after) return 'End Date is required.';
      return '';
    },
  },
  methods:{
    filterMedia(array, medium) {
      return array.filter(function (el) {
        return el.medium === medium;
      });
    },
    submitForm(){
      axios.get('/api/v1/news', {
        params: {
          query: this.query,
          before: moment(this.before).format('x'),
          after: moment(this.after).format('x'),
          interval: this.interval,
        },
      }).then((response) => {
        this.loaded = false;
        this.labels = [];
        this.chartData = [];
        const data = response.data;

        let medium = {
          'TV': [],
          'PRINT': [],
          'ONLINE': [],
          'RADIO': [],
          'SOCIAL': []
        };

        for (var key in data) {
          this.labels.push(key);
          Object.keys(medium).forEach(type => {
            medium[type].push(this.filterMedia(data[key], type).length);
          });
        }
        Object.keys(medium).forEach(type => {
          this.chartData.push(
            {
              label: type,
              backgroundColor: this.colours[type],
              data: medium[type]
            }
          )
        });
        this.loaded = true;
      });
    }
  }
}
</script>

<template>
  <div>
    <form v-on:submit.prevent="submitForm">
      <div class="form-group">
        <b-form-textarea
          id="query"
          v-model="query"
          placeholder="Enter something..."
          rows="3"
          max-rows="6"
          required
        ></b-form-textarea>
      </div>

      <div class="row">
        <div class="col-md-3">
          <label for="after">Start Date</label>
          <date-picker v-model="after" id="after" type="date" :format="dateFormat" required></date-picker>
        </div>

        <div class="col-md-3">
          <label for="before">End Date</label>
          <date-picker v-model="before" id="before" type="date" :format="dateFormat" required></date-picker>
        </div>
      </div>

      <div class="mt-4 text-center">
        <label>Interval</label>
        <b-form-input id="interval" v-model="interval" type="range" min="1" max="31"></b-form-input>
        <div class="mt-1">{{ interval }}d</div>
      </div>

      <div class="mt-5 mb-5 text-center">
        <button class="btn btn-primary">Search</button>
      </div>
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
      query: '',
      before: new Date('2019-08-30'),
      after:  new Date('2019-08-01'),
      interval: 1,
      colours: {
        'Online': '#f87979',
        'Radio':  '#3D5B96',
        'TV':     '#1EFFFF',
        'Print':  '#3b662c',
        'Social': '#adab39'
      },
      dateFormat: 'DD/MM/YYYY'
    }
  },
  methods:{
    docCount(array, medium) {
      let doc_count = 0;
      let doc = array.find(element => element.key === medium);
      if (doc !== undefined) {
        doc_count = doc.doc_count;
      }
      return doc_count;
    },
    submitForm(){
      axios.get('/api/v1/news', {
        params: {
          query: this.query,
          before: moment(this.before).format('x'),
          after: moment(this.after).format('x'),
          interval: this.interval + 'd',
        },
      }).then((response) => {
        this.loaded = false;
        this.labels = [];
        this.chartData = [];
        const data = response.data;

        let medium = {
          'Online': [],
          'Radio':  [],
          'TV':     [],
          'Print':  [],
          'Social': [],
        };

        for (var item in data) {
          let formattedDate = new Date(data[item]['label']).toLocaleDateString();
          this.labels.push(formattedDate);

          Object.keys(medium).forEach(type => {
            medium[type].push(this.docCount(data[item]['data'], type));
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

<style>
.btn-primary,
.btn-primary:hover,
.btn-primary:active,
.btn-primary:visited {
  background-color: black !important;
  border-color: black;
}
</style>
<template>
  <div>
    <form v-on:submit.prevent="submitForm">
      <div class="form-group">
        <label for="query">Query</label>
        <input type="text" class="form-control" id="query" placeholder="Query" v-model="query">
      </div>

      <div class="form-group">
        <label for="before">Start Date</label>
        <date-picker v-model="before" id="before" type="datetime"></date-picker>
      </div>

      <div class="form-group">
        <label for="after">End Date</label>
        <date-picker v-model="after" id="after" type="datetime"></date-picker>
      </div>

      <div class="form-group">
        <label for="interval">Range</label>
        <input type="range" name="range" class="form-control-range" id="interval" min="1" max="30" v-model="interval">
        <span v-text="interval">daaaa</span>
      </div>

      <div class="form-group">
        <button class="btn btn-primary">Submit</button>
      </div>

      <p class="text-red-600 text-xs italic mt-1" v-if="errorMessage">
        {{ errorMessage }}
      </p>
    </form>
  </div>
</template>

<script>
import axios from 'axios';
import DatePicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';
import moment from 'moment'

export default {
  name: 'PostFormAxios',
  components: { DatePicker },
  data(){
    return {
      query: null,
      before: null,
      after: null,
      interval: 1
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
    submitForm(){
      axios.get('/api/v1/news', {
        params: {
          query: this.query,
          before: moment(this.before).format('x'),
          after: moment(this.after).format('x'),
          interval: this.interval,
        },
      }).then((response) => {
          console.log(response.data);
          console.log(response.status);
          console.log(response.statusText);
          console.log(response.headers);
          console.log(response.config);
        }).catch(() => {
          // error.response.status Check status code
        }).finally(() => {
        //Perform action in always
      });
    }
  }
}
</script>

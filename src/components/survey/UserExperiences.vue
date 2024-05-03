<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="!isLoading && error"> {{error}}</p>
      <!-- not loading and dont have results or results length ==0  -->
      <p v-else-if="!isLoading && (!results || results.length ===0)">No data found, start adding some experiences.</p>
      <ul v-else-if="!isLoading && results && results.length > 0">
        <!-- if its not loading show the data -->
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data(){
    return {
      results : [],
      isLoading : false,
      error : null
    }
  },
  methods : {
    loadExperiences(){
      this.isLoading = true;
      this.error = null;
       fetch('https://vue-http-demo-c3177-default-rtdb.firebaseio.com/surveys.json').then((response) => {
        if(response.ok){
         return response.json();  //for parsing data we get from database
        }
       }).then((data) =>{
        this.isLoading = false
        const results = [];
        for (const id in data){
          results.push({ id : id, name : data[id].name, rating : data[id].rating})
        }
        this.results = results;
       }).catch((error) => {
        console.log(error);
        this.isLoading = false;
        this.error = 'Failed to fetch data from server - please try again later.';
       })
    }
  },

  mounted(){
    this.loadExperiences();
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
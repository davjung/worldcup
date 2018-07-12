<template>
  <div class="app">
    <h1>World Cup Teams</h1>
    <div class="row">
      <div id="cell" class="col-md-6">
        <input type="text" class="form-control" placeholder="Country" v-model="country">
        <button v-on:click="lookupCountry(country)">Submit</button>
        <br>
        <br>
        <div>Name: {{s_Name}} {{code}}</div>
        <div>Group: {{ group }}</div>
        <div>Wins: {{ wins }}</div>
        <div>Draws: {{ draws }}</div>
        <div>Losses: {{ losses }}</div>
        <div>Points: {{ points }}</div>
        <div>Goal Differential: {{ gd }}</div>

      </div>
    </div>
    <div id="teamlist">
        <h2>List of Teams</h2>
        <div v-for="team in teams">
          {{ team.country }}
        </div>
      </div>
  </div>
</template>



<script>
import _ from 'lodash'
export default {
  name: 'Teams',
  data () {
    return {
      country: '',
      s_Name: '',
      code: '',
      group: '',
      wins: 0,
      draws: 0,
      losses: 0,
      points: 0,
      gd: 0,
      teams: '',
    }
  },

  mounted() {
    this.axios.get('https://worldcup.sfg.io/teams/')
      .then(response => (this.teams = response.data))
  },

  methods: {
    lookupCountry: function(cty) {
      var app = this
      app.s_Name = "Searching..."
      this.axios.get('https://worldcup.sfg.io/teams')
      .then(function (response) {
          var found = null;
          var marker = 0;
          for (var i = 0; i < response.data.length; i++) {
            
            var element = response.data[i];
            if (element.country == cty) {
              found = element;
              app.s_Name = found.country;
              app.code = found.fifa_code;
              app.group = found.group_letter;
              marker = 1;
              break;
            }
          }
          if (marker == 0) {
            app.s_Name = 'Invalid Team';
            app.group = '';
            app.code = '';
          }
      })
      .catch(function (error) {
        app.s_Name = "Invalid Team"
      })

      
      this.axios.get('https://worldcup.sfg.io/teams/results')
      .then(function (response) {
        var found = null;
        var marker = 0;
        for (var j = 0; j < response.data.length; j++) {
          var element = response.data[j];
          if (element.country == app.country) {
            found = element;
            app.wins = found.wins;
            app.draws = found.draws;
            app.losses = found.losses;
            app.points = found.points;
            app.gd = found.goal_differential;
            marker = 1;
            break;
          }
        }
        if (marker == 0) {
          app.wins = 0;
          app.draws = 0;
          app.losses = 0;
          app.points = 0;
          app.gd = 0;
        }
      })
      .catch(error => {
        console.log(error);
      })
    },  
    
    returnList: function() {
      this.axios.get("https://worldcup.sfg.io/teams/")
      .then(function (response) {
        this.teams = response.data
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>

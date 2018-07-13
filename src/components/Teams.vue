<template>
  <div class="app">
    <h1>World Cup Teams</h1>
    <div class="stats">
      <div id="cell" class="col-md-6">
        <form><input type="text" class="form-control" autocomplete="off" placeholder="Country" name="country" v-model="country" v-on:keyup="filterFunction()"></form>
        
        <div class="dropdown">
          
          <div id="myDropdown" ref="myDropdown" class="dropdown-content" >
              <div v-for="team in teams" :key="team.id">
                <button id=buttonval style="display:none" v-on:click="handler(team.country)">{{team.country}}</button>
              </div>
          </div>        
        </div>
        <br>

        <div id="statbox">
          <div>Name: {{s_Name}} {{code}}</div>
          <div>Group: {{ group }}</div>
          <div>Wins: {{ wins }}</div>
          <div>Draws: {{ draws }}</div>
          <div>Losses: {{ losses }}</div>
          <div>Points: {{ points }}</div>
          <div>Goal Differential: {{ gd }}</div>
        </div> <!-- end statbox -->
      </div> <!-- end cell -->
    </div>  <!-- end stats -->
    
    <h2>List of Teams</h2>
    
    <div id="teamlist">

      <div id = 'ad'>
        <div v-for="n in 4" :key="n">
          <div v-for="team in teams" :key="team.country">
            <div v-if="team.group_id == n">
              {{ team.country }}
            </div>
          </div>
          <br>
        </div>
      </div>

      <div id='eh'>
        <div v-for="n in 8" :key="4+n">
          <div v-for="team in teams" :key="team.country">
            <div v-if="team.group_id == 4+n">
              {{ team.country }}
            </div>
          </div>
          <br>
        </div>
      </div>

    </div> <!-- end teamlist -->

  </div> <!-- end app -->

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
      teams: [],
      teams2: [],
    }
  },

  created() {
    this.teams = this.returnList();
    this.teams2 = this.returnList2();
  },

  watch: {
    country: function() {
      if (this.teams2.includes(this.country)){
        console.log()
        this.lookupCountry();
      } else {
        this.clear();
      }
    }
    
  },

  methods: {
    lookupCountry: function() {
      var app = this;
      console.log("api called");
      this.axios.get('https://worldcup.sfg.io/teams/results')
      .then(function (response) {
          var found = null;
          for (var i = 0; i < response.data.length; i++) {            
            var element = response.data[i];
            if (element.country == app.country) {
              found = element;
              app.s_Name = found.country;
              app.code = found.fifa_code;
              app.group = found.group_letter;
              app.wins = found.wins;
              app.draws = found.draws;
              app.losses = found.losses;
              app.points = found.points;
              app.gd = found.goal_differential;
              return;
            }
          }
      })
      .catch(function (error) {
        app.s_Name = "Error: Try again in 60 seconds"
      })
    },  
    
    clear: function() {
      var app = this;
      app.s_Name = '';
      app.group = '';
      app.code = '';
      app.wins = 0;
      app.draws = 0;
      app.losses = 0;
      app.points = 0;
      app.gd = 0;
      return;
    },



    returnList: function() {
      var t = [];
      console.log("api called");
      this.axios.get("https://worldcup.sfg.io/teams/")
      .then(function (response) {
        var a = response.data;
        var length = a.length;
        for (var g = 1; g<9; g++) {  //sort by group
          for (var i = 0; i < length; i++) {
            if (a[i].group_id == g) {
              t.push(a[i]);
            }
          }
        }
      })
      return t;
      console.log(t);
    },

    returnList2: function() {
      var t = [];
      console.log("api called");
      this.axios.get("https://worldcup.sfg.io/teams/")
      .then(function (response) {
        var a = response.data;
        var length = a.length;
        for (var g = 1; g<9; g++) {  //sort by group
          for (var i = 0; i < length; i++) {
            if (a[i].group_id == g) {
              t.push(a[i].country);
            }
          }
        }
      })
      return t;
      console.log(t);
    },

    filterFunction: function() {
      var input, filter, a, div, i;
      input = this.country;
      console.log(input);
      filter = input.toUpperCase();
      div = document.getElementById("myDropdown"); // div of buttons
      a = div.getElementsByTagName("button");      // list of buttons

      for (i = 0; i < a.length; i++) {
        if (a[i].innerHTML.toUpperCase().indexOf(filter) > -1) {
          a[i].style.display = "block";
        } 
        else {
          a[i].style.display = "none";
        }
        if (filter.length < 2) {
          a[i].style.display = "none";
        }
      }
    },

    updateCountry: function(team) {
      var a = document.getElementById("buttonval").innerHTML;
      document.forms[0].country.value = team;
      this.country = team;
    },

    handler: function(team){
      this.updateCountry(team);
      this.filterFunction();
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

#buttonval {
  width: 250px;
  border-radius: 0%;
  text-align: left;
  margin: 0 auto;
  padding: 5px;
  color: white;
  background-color:	#888888
}

#buttonval:hover {
  background-color: white;
  color: black;
  font-weight: bold;
}

input {
  width: 300px;
  padding: 5px;
  margin-bottom: 5px;
}

#teamlist {
  width: 300px;
  margin: 0 auto;
}

#ad {
  float: left;
  width: 150px;
  margin: 0;
}
#eh {
  float: left;
  width: 150px;
  margin:0;
}

#statbox {
  margin: auto;
  width: 276px;
  padding: 15px;
  border: 2px solid black;
}
</style>

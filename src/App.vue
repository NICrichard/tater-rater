<template>
  <div id="app" class="container">
    <div class="row text-center"><h2>How would you rate us?</h2></div>
        <div class="row text-center">
            <i :class="{'flaticon-happiness':true, 'selected':(isActive === 1)}" @click="clicked('happy')"></i>
            <i :class="{'flaticon-meh':true, 'selected':(isActive === 2)}" @click="clicked('meh')"></i>
            <i :class="{'flaticon-sad':true, 'selected':(isActive === 3)}" @click="clicked('sad')"></i>
        </div>
        <div class="row" v-show="showMe"><textarea class="form-control" rows="3" id="feedbackText" placeholder="enter your additional feedback here" @focus="msg"></textarea></div>
        <div id="response" v-show="final"></div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.min.css";
import "jquery/src/jquery.js";
import "bootstrap/dist/js/bootstrap.min.js";
import axios from "axios";
let $ = jQuery;
export default {
  name: "app",
  data() {
    return {
      showMe: false,
      mood: "",
      isActive: 0,
      final: false,
      id: "",
      token: ""
    };
  },
  methods: {
    guidGenerator: function() {
      var S4 = function() {
        return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
      };
      return S4() + S4() + "-" + S4() + "-" + S4() + "-" + S4() + S4() + S4();
    },
    clicked: function(mood) {
      let self = this;
      if (mood === "happy") {
        this.isActive = 1;
      } else if (mood === "meh") {
        this.isActive = 2;
      } else if (mood === "sad") {
        this.isActive = 3;
      } else {
        this.isActive = 0;
      }
      this.mood = mood;
      this.showMe = true;
      var myMood = mood;
      setTimeout(function() {
        self.callOut();
      }, 3000);
    },
    msg: function() {
      let self = this;
      var myMood = this.mood.toString();
      this.final = true;
      setTimeout(function() {
        var msg =
          "<p>The user said they were <strong>" +
          myMood +
          "</strong> about the app.</p>";
        if ($("textarea#feedbackText").val() !== "") {
          msg +=
            "<p>Their additional feedback:<br><em>" +
            $("textarea#feedbackText").val() +
            "</em></p>";
        } else {
          msg += "<p>No additional feedback left.</p>";
        }
        document.getElementById("response").innerHTML = msg;
      }, 1500);
      setTimeout(function() {
        self.callOut();
      }, 1500);
    },
    callOut: function() {
      var headers = {
        "Content-Type": "application/json;charset=UTF-8",
        "Access-Control-Allow-Origin": "*",
        "X-Content-Type-Options": "nosniff"
      };
      axios
        .post(
          "https://y1dtrwmk40.execute-api.us-east-1.amazonaws.com/UAT/RateMyTater-test",
          {
            id: this.id,
            token: this.token,
            rating: this.mood.toString(),
            text: $("textarea#feedbackText").val()
          },
          headers
        )
        .then(response => {
          console.log(response);
        })
        .catch(e => {
          console.log("ERROR: " + e);
        });
    },
    tokenGetter: function() {
      // if (localStorage.getItem("accessidaho")) {
      //  this.token = localStorage.getItem("accessidaho");
      // } else {
      var rand = Math.random()
        .toString(36)
        .substr(2);
      var token = rand + rand;
      localStorage.setItem("accessidaho", token);
      return token;
      // }
    }
  },
  created() {
    this.id = this.guidGenerator();
    this.token = this.tokenGetter();
  }
};
</script>

<style scoped>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

@import "icons/flaticon.css";

.container {
  width: 500px;
  height: auto;
  border: 3px solid black;
  background-color: azure;
  padding: 20px;
  margin: auto;
  text-align: center;
}

.feedback {
  padding-top: 20px;
}

textarea {
  resize: none;
}

.hide {
  display: none;
}

.foot {
  margin-top: 10px;
  margin-right: 15px;
}

.selected {
  color: #012b72;
}

#response {
  margin: auto;
  width: 50%;
  height: auto;
  background-color: #efefef;
  color: #000;
  font-size: 1.5em;
  padding: 20px;
  display: block;
  margin-top: 20px;
}
</style>

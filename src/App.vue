<template>
  <div id="app" class="container">
    <div v-show="nonReviewer" class="row text-center">
      <div class="col-12"><h2>How would you rate us?</h2></div>
          <div class="row">
              <div class="col-md-4"><i :class="{'flaticon-happiness':true, 'selected':(isActive === 1)}" @click="clicked('happy')"></i></div>
              <div class="col-md-4"><i :class="{'flaticon-meh':true, 'selected':(isActive === 2)}" @click="clicked('meh')"></i></div>
              <div class="col-md-4"><i :class="{'flaticon-sad':true, 'selected':(isActive === 3)}" @click="clicked('sad')"></i></div>
          </div>
          <div class="row" v-show="showMe">
            <div class="col-xs-12">
                <label class="sr-only" for="feedback comments">Feedback comments</label><textarea class="form-control" rows="3" id="feedbackText" placeholder="enter your additional feedback here" @focus="msg" aria-label="feedback comments"></textarea>
            </div>
          </div>
      </div>
    <div v-show="!nonReviewer" class="row text-center"><h2>Thanks, you've already reviewed the app!</h2></div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.min.css";
import "jquery/src/jquery.js";
import "bootstrap/dist/js/bootstrap.min.js";
import axios from "axios";
var $ = jQuery;
export default {
  name: "app",
  data: function() {
    return {
      showMe: false,
      mood: "",
      isActive: 0,
      token: "",
      nonReviewer: false
    };
  },
  methods: {
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
      var cur = new Date();
      var after30 = cur.setDate(cur.getDate() + 30);
      var rand = Math.random()
        .toString(36)
        .substr(2);
      var token = rand + rand;
      localStorage.setItem("accessidaho", token);
      localStorage.setItem("ai-exp", after30);
      this.token = token;
      setTimeout(function() {
        self.callOut();
      }, 1500);
    },
    msg: function() {
      var self = this;
      var myMood = this.mood.toString();
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
            token: this.token,
            rating: this.mood.toString(),
            text: $("textarea#feedbackText").val()
          },
          headers
        )
        .then(response => {})
        .catch(e => {
          console.log("ERROR: " + e);
        });
    },
    tokenGetter: function() {
      var cur = new Date();
      if (
        localStorage.getItem("accessidaho") &&
        localStorage.getItem("ai-exp") >= cur.getDate() + 30
      ) {
        this.token = localStorage.getItem("accessidaho");
      } else {
        this.nonReviewer = true;
      }
    }
  },
  created: function() {
    this.tokenGetter();
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
  max-width: 500px;
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

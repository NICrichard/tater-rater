<template>
  <div id="app" class="container">
    <div class="row text-center">
      <div class="col-12"><h1 class="question">How would you rate your experience?</h1></div>
          <div class="row">
              <div class="col-md-4"><i :class="{'flaticon-smiley':true, 'selected':(isActive === 1)}" @click="clicked('happy')"></i></div>
              <div class="col-md-4"><i :class="{'flaticon-anger':true, 'selected':(isActive === 2)}" @click="clicked('meh')"></i></div>
              <div class="col-md-4"><i :class="{'flaticon-sad-smiley':true, 'selected':(isActive === 3)}" @click="clicked('sad')"></i></div>
          </div>
          <div class="row" v-show="showMe">
            <div class="col-xs-12">
                <label class="sr-only" for="feedback comments">Feedback comments</label><textarea class="form-control" rows="3" id="feedbackText" placeholder="enter your additional feedback here" @focus="msg" aria-label="feedback comments"></textarea>
            </div>
          </div>
      </div>
  </div>
</template>

<script>
// import "bootstrap/dist/css/bootstrap.min.css";
// import "jquery/src/jquery.js";
// import "bootstrap/dist/js/bootstrap.min.js";
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
      changes: ""
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
      // var myMood = mood;
      setTimeout(function() {
        if (this.changes >= 0) {
          this.changes = this.changes + 1;
        }
        self.callOut();
      }, 2000);
    },
    msg: function() {
      var self = this;
      // var myMood = this.mood.toString();
      setTimeout(function() {
        if (this.changes >= 0) {
          this.changes = this.changes + 1;
        }
        self.callOut();
      }, 2000);
    },
    callOut: function() {
      var headers = {
        "Content-Type": "application/json;charset=UTF-8",
        "Access-Control-Allow-Origin": "*",
        "X-Content-Type-Options": "nosniff"
      };
      if (this.token !== "") {
        axios
          .post(
            "https://y1dtrwmk40.execute-api.us-east-1.amazonaws.com/UAT/RateMyTater-test",
            {
              tater_id: this.token,
              rating: this.mood.toString(),
              text: $("textarea#feedbackText").val(),
              ip: "127.0.0.1",
              referrer: "the_rainbow",
              app: "testing_app",
              changes: this.changes
            },
            headers
          )
          .then(response => {
            // console.log(response);
            this.token = response.data.tater_id;
            localStorage.setItem("accessidaho", this.token);
          })
          .catch(e => {
            console.error(e);
          });
      } else {
        axios
          .post(
            "https://y1dtrwmk40.execute-api.us-east-1.amazonaws.com/UAT/RateMyTater-test",
            {
              rating: this.mood.toString(),
              text: $("textarea#feedbackText").val(),
              ip: "127.0.0.1",
              referrer: "the_rainbow",
              app: "testing_app"
            },
            headers
          )
          .then(response => {
            // console.log(response);
            this.token = response.data.tater_id;
            this.changes = response.data.changes;
            localStorage.setItem("accessidaho", this.token);
          })
          .catch(e => {
            console.error(e);
          });
      }
    },
    tokenGetter: function() {
      if (localStorage.getItem("accessidaho")) {
        this.token = localStorage.getItem("accessidaho");
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

.question {
  font-size: 2.5rem;
}
</style>

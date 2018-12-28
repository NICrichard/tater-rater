<template>
  <div id="app" class="modalcontainer">
    <div class="row">
      <div class="col-12 text-right">
        <button class="btn btn-secondary">x</button>
      </div>
    </div>
    <div v-show="nonReviewer" class="row text-center">
      <div class="col-12">
        <h1 class="question">How would you rate your experience?</h1>
      </div>
      <div class="col-12">
        <div class="row">
          <div class="col-md-4">
            <i
              :class="{'flaticon-smile':true, 'selected':(isActive === 1)}"
              @click="clicked('happy')"
            ></i>
          </div>
          <div class="col-md-4">
            <i
              :class="{'flaticon-emoticon':true, 'selected':(isActive === 2)}"
              @click="clicked('meh')"
            ></i>
          </div>
          <div class="col-md-4">
            <i :class="{'flaticon-sad':true, 'selected':(isActive === 3)}" @click="clicked('sad')"></i>
          </div>
        </div>
      </div>
      <div class="row" v-show="showMe">
        <div class="col-xs-12">
          <label class="sr-only" for="feedback comments">Feedback comments</label>
          <textarea
            class="col form-control modaltextarea"
            rows="3"
            id="feedbackText"
            placeholder="enter your additional feedback here"
            @focus="msg"
            aria-label="feedback comments"
          ></textarea>
        </div>
      </div>
    </div>
    <div v-show="!nonReviewer" class="row text-center">
      <h2>Thanks for your review!</h2>
    </div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.min.css";
import jQuery from "jquery/src/jquery.js";
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
      nonReviewer: false,
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
      setTimeout(function() {
        if (this.changes >= 0) {
          this.changes = this.changes + 1;
        }
        self.callOut();
      }, 2000);
    },
    msg: function() {
      var self = this;
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
            console.log(response);
            this.token = response.data.tater_id;
            localStorage.setItem("accessidaho", this.token);
          })
          .catch(e => {
            console.log("ERROR: " + e);
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
            console.log(response);
            this.token = response.data.tater_id;
            this.changes = response.data.changes;
            localStorage.setItem("accessidaho", this.token);
          })
          .catch(e => {
            console.log("ERROR: " + e);
          });
      }
    },
    tokenGetter: function() {
      if (localStorage.getItem("accessidaho")) {
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

.modalcontainer {
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

.modaltextarea {
  resize: none;
}

.hide {
  display: none;
}

.foot {
  margin-top: 10px;
  margin-right: 15px;
}

.row {
  -ms-flex-wrap: wrap;
}

.selected {
  color: #012b72;
}

.question {
  font-size: 2.5rem;
}

.flaticon-emoticon,
.flaticon-sad,
.flaticon-smile {
  cursor: pointer;
}

.form-control {
  text-align: center !important;
  margin-left: 30px !important;
  width: 450px !important;
}
</style>

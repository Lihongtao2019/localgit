<template>
  <body>
    <header>
      <div class="inputbox" id="iHour" v-show="isRunning === 0">
        <input-content name="hour" info="" />
        <p>时</p>
      </div>
      <div class="inputbox" id="iMinute" v-show="isRunning === 0">
        <input-content name="minute" info="" />
        <p>分</p>
      </div>
      <div class="inputbox" id="iecond" v-show="isRunning === 0">
        <input-content name="second" info="" />
        <p>秒</p>
      </div>
      <spe-button
        @option="Countup"
        class="btn"
        id="countup"
        name="开始正计时"
        v-show="isRunning === 0"
      />
      <spe-button
        @option="Countdown"
        class="btn"
        id="countdown"
        name="开始倒计时"
        v-show="isRunning === 0"
      />

      <div class="str">
        <label class="small" id="hint" v-show="isRunning === 1"
          >正在倒计时&nbsp;00:00:00</label
        >
      </div>
      <spe-button
        @option="Pause"
        class="subbtn"
        id="pause"
        name="暂停计时器"
        v-show="isRunning === 1 && isPause === 0"
      ></spe-button>
      <spe-button
        @option="Resume"
        class="subbtn"
        id="resume"
        name="恢复计时器"
        v-show="isRunning === 1 && isPause === 1"
      ></spe-button>
      <spe-button
        @option="Clear"
        class="subbtn"
        id="clear"
        name="清空倒计时"
        v-show="isRunning === 1"
      ></spe-button>
      <spe-button
        @option="Restart"
        class="subbtn"
        id="restart"
        name="重新再计时"
        v-show="isRunning === 1"
      ></spe-button>
    </header>
    <main>
      <div id="timebox">
        <label class="big" id="time">00:00:00</label>
      </div>
    </main>
  </body>
</template>

<script>
import speButton from "./components/speButton.vue";
import inputContent from "./components/inputContent.vue";
export default {
  components: {
    speButton,
    inputContent,
  },
  data: function () {
    return {
      beginnum: 0,
      endnum: 0,
      restartnum: 0,
      choose: 1,
      isPause: 0,
      isRunning: 0,
      flag: "",
      keyboard: document.onkeydown,
    };
  },
  methods: {
    Pause: function () {
      this.isPause = 1;
      clearInterval(this.flag);
    },
    Resume: function () {
      this.isPause = 0;
      this.flag = setInterval(this.clock, 100);
    },
    Clear: function () {
      clearInterval(this.flag);
      document.getElementById("time").innerHTML =
        this.fun(this.endnum, 36000, 12) +
        ":" +
        this.fun(this.endnum, 600, 60) +
        ":" +
        this.fun(this.endnum, 10, 60);
      this.isRunning = 0;
      const hourElement = document.getElementById("hour");
      const minuteElement = document.getElementById("minute");
      const secondElement = document.getElementById("second");
      hourElement.value = "";
      minuteElement.value = "";
      secondElement.value = "";
    },
    Restart: function () {
      this.beginnum = this.restartnum;
    },
    Countup: function () {
      this.choose = 1;
      this.Count();
    },
    Countdown: function () {
      this.choose = 0;
      this.Count();
    },
    Count: function () {
      let hh = parseInt(document.getElementById("hour").value || 0);
      let mm = parseInt(document.getElementById("minute").value || 0);
      let ss = parseInt(document.getElementById("second").value || 0);
      if (ss > 59) ss = 59;
      if (mm > 59) mm = 59;

      this.isRunning = 1;
      if (this.choose === 1) {
        document.getElementById("clear").value = "清除正计时";
        document.getElementById("hint").innerHTML = "正在正计时 ";
        this.beginnum = 0;
        this.endnum = (hh * 3600 + mm * 60 + ss) * 10;
        this.resta = 0;
      } else {
        document.getElementById("hint").innerHTML = "正在倒计时 ";
        this.beginnum = (hh * 3600 + mm * 60 + ss) * 10;
        this.endnum = 0;
        this.resta = this.beginnum;
      }
      if (this.choose === 1) {
        document.getElementById("hint").innerHTML =
          document.getElementById("hint").innerHTML +
          this.fun(this.endnum, 36000, 12) +
          ":" +
          this.fun(this.endnum, 600, 60) +
          ":" +
          this.fun(this.endnum, 10, 60);
      } else {
        document.getElementById("hint").innerHTML =
          document.getElementById("hint").innerHTML +
          this.fun(this.beginnum, 36000, 12) +
          ":" +
          this.fun(this.beginnum, 600, 60) +
          ":" +
          this.fun(this.beginnum, 10, 60);
      }
      this.flag = setInterval(this.clock, 100);
    },
    clock: function () {
      if (this.beginnum !== this.endnum) {
        if (this.choose === 0) {
          this.beginnum--;
        } else {
          this.beginnum++;
        }
        document.getElementById("time").innerHTML =
          this.fun(this.beginnum, 36000, 12) +
          ":" +
          this.fun(this.beginnum, 600, 60) +
          ":" +
          this.fun(this.beginnum, 10, 60);
      } else {
        document.getElementById("pause").style.display = "none";
        document.getElementById("hint").innerHTML =
          document.getElementById("hint").innerHTML + " 已结束";
        clearInterval(this.flag);
      }
    },
    fun: function (num1, num2, mod) {
      let num = Math.floor(num1 / num2) % mod;
      if (num < 10) {
        return "0" + num;
      } else {
        return num + "";
      }
    },
  },
  mounted: function () {
    let that = this;
    document.onkeydown = function (event) {
      if (event.keyCode === 32) {
        console.log(that.isPause);
        if (that.isPause === 1) {
          console.log("你点击了恢复按钮");
          that.Resume();
        } else {
          that.Pause();
        }
      }
      if (event.keyCode === 13) {
        if (that.choose === 1) {
          that.Countup();
        } else {
          that.Countdown();
        }
      }
    };
  },
};
</script>

<style>
header {
  position: absolute;
  left: 0;
  top: 0;
  height: 70px;
  width: 1220px;
  background: #97a5bc;
  box-shadow: 0px 2px 4px 0px rgba(0, 0, 0, 0.1);
}
main {
  position: absolute;
  left: 0;
  top: 70px;
  height: 440px;
  width: 1220px;
  background: white;
  box-shadow: 0px 2px 4px 0px rgba(0, 0, 0, 0.1);
}
div.inputbox {
  position: relative;
  background: #ffffff;
  box-shadow: 0px 2px 4px 0px rgba(0, 0, 0, 0.1);
  width: 70px;
  height: 40px;
  border-radius: 5px;
  left: 20px;
  top: 15px;
  float: left;
  margin-left: 20px;
}
p {
  position: relative;
  width: 32px;
  height: 22px;
  right: 1px;
  bottom: 7px;
  font-size: 16px;
  font-family: PingFangSC-Regular;
  text-align: center;
  float: right;
}
input {
  position: relative;
  width: 40px;
  height: 22px;
  left: 7px;
  top: 8px;
  text-align: center;
  font-family: PingFangSC-Regular;
  font-size: 16px;
  border-style: none;
  float: left;
}
button.btn {
  position: relative;
  background-color: #2188e9;
  font-family: PingFangSC-Regular;
  font-size: 16px;
  color: #ffffff;
  box-shadow: 0px 2px 4px 0px rgba(0, 0, 0, 0.1);
  width: 98px;
  height: 40px;
  border-radius: 5px;
  float: left;
  top: 15px;
  left: 20px;
  margin-left: 20px;
}
div#timebox {
  position: relative;
  width: 960px;
  height: 200px;
  text-align: center;
  top: 127px;
  left: 131px;
  font-size: 200px;
  font-family: PTMono-Bold;
}
label.big {
  position: relative;
  left: -1px;
  bottom: 15px;
  color: #333333;
}
div.str {
  position: relative;
  float: left;
  width: 227px;
  height: 100%;
  top: 24px;
  left: 40px;
  font-size: 16px;
  color: white;
}
button.subbtn {
  width: 98px;
  height: 40px;
  border-radius: 5px;
  top: 15px;
  font-family: PingFangSC-Regular;
  font-size: 16px;
  color: #ffffff;
}
button#pause,
button#resume {
  position: relative;
  left: 20px;
  background-color: #2188e9;
  float: left;
}
button#clear {
  position: relative;
  margin-left: 670px;
  background-color: #dd2e1d;
  float: left;
}
button#restart {
  position: relative;
  margin-left: 20px;
  background-color: #ffb020;
  float: left;
}
</style>
<template>
  <div class="home">
    <div class="userHomePage">
      <el-row>
        <div class="name-text-div">
          <h1>{{ nameText }}</h1>
        </div>
      </el-row>
      <el-row>
        <div>
          <h2>{{ roleText }}</h2>
        </div>
      </el-row>
      <el-row>
        <div class="group-text-div">
          <h3>{{ groupText }}</h3>
        </div>
      </el-row>
      <el-row>
        <div class="status-text-div">
          <h3>{{ statusText }}</h3>
        </div>
      </el-row>
    </div>
    <div style="flex-basis: 40%">
      <el-carousel height="500px">
        <el-carousel-item v-for="(item, index) in img_list" :key="index">
          <h2 style="text-align: center">Contributor: {{ item.name }}</h2>
          <img style="margin-left: 150px" :src="item.img"/>
        </el-carousel-item>
      </el-carousel>
    </div>
    <div style="flex-basis: 30%">
      <el-card style="text-align: left;width:70%;margin:auto" class="wrapperCard">
        <h3>用户在线列表</h3>
        <el-scrollbar height="450px">
          <div v-for="(item, index) in onlineUsers" :key="index">
            <el-card class="showCard">
              {{ index + 1 }}. &nbsp {{ item.name }}
            </el-card>
          </div>
        </el-scrollbar>
      </el-card>
    </div>

  </div>
</template>

<script>
import img1 from "@/assets/dz.png";
import img2 from "@/assets/lsm.png";
import img3 from "@/assets/fdz.png";
import img4 from "@/assets/xyw.png";
import img5 from "@/assets/siri.png";

export default {
  name: "UserHomePage",

  data() {
    const img1 = require("../../assets/dz.png");
    const img2 = require("../../assets/lsm.png");
    const img3 = require("../../assets/fdz.png");
    const img4 = require("../../assets/xyw.png");
    const img5 = require("../../assets/siri.png");

    return {
      img_list: [
        {
          name: "Ding Zhe",
          img: img1
        },
        {
          name: "Luo Shimin",
          img: img2
        },
        {
          name: "Feng Dazhi",
          img: img3
        },
        {
          name: "Xiao Yuwei",
          img: img4
        },
        {
          name: "Li Sirui",
          img: img5
        }
      ],
      user: {},
      nameText: "",
      roleText: "",
      groupText: "",
      statusText: "",
      onlineUsers: [],
    }
  },

  mounted() {
    let state = JSON.parse(sessionStorage.getItem("state"));
    this.user = JSON.parse(JSON.stringify(state.user));
    console.log(this.user);
    this.startTypingAnimation();
    this.getAllOnlineUsers()
  },

  methods: {
    getAllOnlineUsers() {
      let _this = this;
      _this.$api.userAPI.getOnlineUsers().then(resp => {
        _this.onlineUsers = resp.data.data.online_users
        console.log(resp);
      }).catch(err => {
        console.log(err);
      });

      setInterval(() => {
        _this.$api.userAPI.getOnlineUsers().then(resp => {
          _this.onlineUsers = resp.data.data.online_users
          console.log(resp);
        }).catch(err => {
          console.log(err);
        })
      }, 10000);
    },
    handleCarouselClick(index) {
      if (index === 0) {
        this.$router.push({path: "/"});
      } else {
        this.$router.push({path: "/userLogin"});
      }
    },
    generateText2Type() {
      let textToType = [];

      let nameString = "你好, " + this.user.name + "。\n";
      textToType.push(nameString);

      let roleString = "你是一名" + this.user.role + "。\n";
      textToType.push(roleString);

      let groupString = "你所在的课题组为:\n\n";
      for (const groupNameKey of this.user.groupName) {
        groupString += "· " + groupNameKey + "\n";
      }
      textToType.push(groupString);

      let statusString = "";
      if (this.user.status === "normal") {
        statusString += "你现在能够使用的功能有：\n\n"
            + "1. 申请报销\n\n"
            + "2. 查看课题组信息\n\n"
            + "3. 查看并管理经费\n\n"
            + "4. 查看报销申请\n\n"
            + "5. 查看并修改个人信息\n\n";
      } else if (this.user.status === "blocked") {
        statusString += "你现在能够使用的功能有：\n\n"
            + "1. 查看课题组信息\n\n"
            + "2. 查看并管理经费\n\n"
            + "3. 查看报销申请\n\n"
            + "4. 查看并修改个人信息\n\n";
            + "但你无法申请报销。\n\n";
      }
      textToType.push(statusString);

      return textToType;
    },

    startTypingAnimation() {
      const textToType = this.generateText2Type();
      let currentText = 0;
      let currentTextIndex = 0;

      const typingInterval = setInterval(() => {
        switch (currentText) {
          case 0:
            this.nameText += textToType[currentText][currentTextIndex++];
            break;
          case 1:
            this.roleText += textToType[currentText][currentTextIndex++];
            break;
          case 2:
            this.groupText += textToType[currentText][currentTextIndex++];
            break;
          case 3:
            this.statusText += textToType[currentText][currentTextIndex++];
            break;
          default:
            break;
        }

        if (currentTextIndex === textToType[currentText].length) {
          currentText++;
          currentTextIndex = 0;
        }

        if (currentText >= textToType.length) {
          clearInterval(typingInterval);
        }
      }, 50); // Adjust the typing speed by changing the interval delay (in milliseconds)
    }
  },
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Ma+Shan+Zheng&display=swap');
@import url('https://fonts.googleapis.com/css2?family=ZCOOL+XiaoWei&display=swap');

.home {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
  height: 93vh;
}

.userHomePage {
  font-family: 'ZCOOL XiaoWei', sans-serif;
  display: grid;
  place-items: center;
  align-items: center;
  flex-basis: 30%;
}

.name-text-div {
  font-family: 'Ma Shan Zheng', cursive;
}

.group-text-div {
  white-space: pre-line;
}

.status-text-div {
  white-space: pre-line;
}

.wrapperCard {
  border: 1px solid #dccfcf;
  box-shadow: 0 0 25px #909399;
  border-radius: 20px;
  background-color: rgba(255, 255, 255, 0.1);
}

.showCard {
  border: 1px solid #dccfcf;
  box-shadow: 0 0 25px #909399;
  border-radius: 20px;
  background-color: rgba(255, 255, 255, 0.75);
  margin-bottom: 1.5%;
}
</style>
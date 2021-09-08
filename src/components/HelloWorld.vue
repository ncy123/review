<template>
  <h2>评论</h2>
  <textarea placeholder="评论一下" name="" id="recontent" cols="60" rows="5"></textarea><br />
  <button @click="reviewClick" id="btn">确定</button>
  <p class="name addreview" @click="addreview" style="color: blue; font-size:15px">评论</p>
  <div class="contents">
    <el-collapse v-model="activeNames" @change="handleChange">
      <div v-for="(item, index) in state.allReview" :key="index" class="allcont">  
          <div>
            <span class="name">{{item.name}}:</span>
            <span class="delete" @click="deleteClick(index)">删除</span> <br>
            <span class="cont">{{item.content}}<span class="replay" @click="replayClick(index)">回复</span></span>
            <span class="time">{{item.time}}</span>
            <div v-for="(item1, in1) in item.replay" :key="in1" style="font-size: 13px">
              <span style="magin-right:5px, ">{{item1.name1}}</span>
              <span style="color:black"> 回复 </span>
              <span>{{item1.name2}}</span>:
              <span>{{item1.content}}</span>
              <span class="time">{{item1.time}}</span>
            </div>
          </div>
      </div>
    </el-collapse>
  </div>
</template>

<script setup>
import { reactive, ref } from "vue";
import { getNowtime } from "../assets/js/time.js";

const n = ref("0");
const state = reactive({
  name: "楼",
  content: "",
  allReview: [],
  isreview: true,
});

state.allReview =
  window.JSON.parse(window.localStorage.getItem("allReview")) || [];

//点击回复
const replayClick = (index) => {
  state.isreview = false;
  console.log(state.allReview[index]);
  document.getElementById("recontent").placeholder =
    "回复" + state.allReview[index].name + "：";
};

//删除评论
const deleteClick = (index) => {
  console.log(index);
  state.allReview.splice(index, 1);
  window.localStorage.setItem(
    "allReview",
    window.JSON.stringify(state.allReview)
  );
};
//点击评论按钮
const addreview = () => {
  state.isreview = true;
  document.getElementById("recontent").placeholder = "评论一下吧";
};

//点击确定评论按钮
const reviewClick = () => {
  var value = document.getElementById("recontent").value.trim();
  if (!value.length) {
    alert("评论不能为空", "提示", {
      confirmButtonText: "确定",
    });
    return;
  }
  var res = window.localStorage.getItem("allReview");
  //当前处于评论状态
  if (state.isreview) {
    if (res === null) {
      const data = {
        name: 1 + "楼",
        content: value,
        time: getNowtime(),
        replay: [],
      };
      let arr = [];
      arr.push(data);
      state.allReview = JSON.parse(JSON.stringify(arr));
      window.localStorage.setItem("allReview", window.JSON.stringify(arr));
    } else {
      res = JSON.parse(res);
      const data = {
        name: res.length + 1 + "楼",
        content: value,
        time: getNowtime(),
        replay: [],
      };
      res.unshift(data);
      console.log(state.allReview);
      state.allReview = JSON.parse(JSON.stringify(res));
      console.log(state.allReview);
      window.localStorage.setItem("allReview", window.JSON.stringify(res));
    }
  } else {
    //当前处于回复状态
    let index = Number(document.getElementById("recontent").placeholder[2]) - 1;
    res = JSON.parse(res);
    res.reverse();
    const data = {
      name1: "匿名用户",
      name2: res[index].name,
      content: value,
      time: getNowtime(),
    };
    res[index].replay.push(data);
    console.log(res);
    res.reverse();
    window.localStorage.setItem("allReview", window.JSON.stringify(res));
  }
  //document.getElementById("recontent").value = " ";
  //document.getElementById("recontent").placeholder = "评论一下吧";
};
</script>

<style lang="less" scoped>
.allcont {
  margin-top: 10px;
  width: 450px;
  background-color: rgb(252, 236, 236);
  .name {
    color: blue;
  }
  .delete {
    font-size: 12px;
    display: inline-block;
    float: right;
  }
  .cont {
    padding-left: 10px;
  }
  .replay {
    font-size: 12px;
    color: rgb(0, 89, 255);
    margin-left: 15px;
  }
  .time {
    display: inline-block;
    float: right;
    font-size: 14px;
  }
}
</style>




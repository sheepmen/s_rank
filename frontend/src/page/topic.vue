<template>
  <div id="Topic">
    <head-menu></head-menu>
    <el-row :gutter="20" type="flex">
      <el-col :span="18">
        <br/>
        <br/>
        <el-row class="flex">
          <el-col :span="20">
            <el-pagination
              @current-change="getTopicList"
              :current-page.sync="currentPage"
              :page-size="10"
              layout="total, prev, pager, next"
              :total="totalTopic">
            </el-pagination>
          </el-col>
          <el-col :span="4">
            <el-input @change="getTopicList" v-model="search" placeholder="搜索" icon="search" style="float: right;"></el-input>
          </el-col>
        </el-row>
        <div v-for="item in topicList">
          <br/>
          <el-card class="box-card">
            <el-row type="flex">
              <el-col :span="6">{{ item.name }}</el-col>
              <el-col :span="6">{{ item.desc }}</el-col>
              <el-col :span="6">{{ item.create_time  | formatDate }}</el-col>
              <el-col :span="6">
                <el-button v-if="!item.follow" type="primary" @click="followTopic(item.id)">关注</el-button>
                <el-button v-else @click="unFollowTopic(item.id)">取关</el-button>
              </el-col>
            </el-row>
          </el-card>
        </div>
      </el-col>
      <el-col :span="6">
        <right-card></right-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import headMenu from "../components/headMenu";
import rightCard from "../components/rightCard";
import api from "../api";
import util from "../util";
// console.log(api.getTopicList())

export default {
  name: "Topic",
  data() {
    return {
      currentPage: 1,
      topicList: [],
      search: "",
      totalTopic: 0
    };
  },
  components: {
    headMenu,
    rightCard
  },
  methods: {
    getTopicList() {
      api
        .getTopicList(this.currentPage-1, 10, this.search)
        .then(response => {
          var status = response.data.status;
          if (status.status_code != 0) {
            Message({
              message: status.status_reason,
              type: "error"
            });
          } else {
            this.topicList = response.data.result.items;
            this.totalTopic = response.data.result.total;
          }
        })
        .catch(function(error) {
          this.$message({
            message: "系统错误，请联系管理员",
            type: "error"
          });
        });
    },
    followTopic(topicId) {
      if (api.isLogin()) {
        api
          .follow(topicId)
          .then(response => {
            var status = response.data.status;
            if (status.status_code != 0) {
              this.$message({
                message: status.status_reason,
                type: "warning"
              });
            } else {
              this.$message({
                message: "关注成功!",
                type: "success"
              });
              this.getTopicList();
            }
          })
          .catch(function(error) {
            this.$message({
              message: "系统错误，请联系管理员",
              type: "error"
            });
          });
      } else {
        this.$message("傻孩子，先去登录吧！");
      }
    },
    unFollowTopic(topicId) {
      if (api.isLogin()) {
        api
          .unFollow(topicId)
          .then(response => {
            var status = response.data.status;
            if (status.status_code != 0) {
              this.$message({
                message: status.status_reason,
                type: "warning"
              });
            } else {
              this.$message({
                message: "取消成功!",
                type: "success"
              });
              this.getTopicList();
            }
          })
          .catch(function(error) {
            this.$message({
              message: "系统错误，请联系管理员",
              type: "error"
            });
          });
      } else {
        this.$message("傻孩子，先去登录吧！");
      }
    }
  },
  mounted() {
    this.getTopicList();
  }
};
</script>
<style>

</style>

<template>
  <div class="PostList">
    <!--在数据未返回的时候，显示这个正在加载的gif-->
    <div class="loading" v-if="isLoading">
      <img src="../assets/loading.gif">
    </div>
    <!--代表我門的主题帖子列表-->
    <div class="posts" v-else>
      <ul>
        <li>
          <div class="toobar">
            <button @click="choseCategory()">全部</button>
            <button @click="choseCategory(category='good')">精华</button>
            <button @click="choseCategory(category='share')">分享</button>
            <button @click="choseCategory(category='ask')">问答</button>
            <button @click="choseCategory(category='job')">招聘</button>
          </div>
        </li>
        <li v-for="post in posts">
          <!--头像-->
          <router-link :to="{
          name:'user_info',
          params:{
            name:post.author.loginname
          }
        }">
            <img :src="post.author.avatar_url" :title="post.author.loginname">
          </router-link>
          <!--回复/浏览-->
          <span class="allcount">
          <span class="reply_count">{{ post.reply_count }}</span>
          /{{ post.visit_count }}
        </span>
          <!--帖子的分类-->
          <span :class="[{put_good:(post.good  === true),put_top:(post.top  === true),
        'topiclist-tab':(post.good  !== true && post.top  !== true)}]">
          <span>
            {{ post | tabFormatter }}
          </span>
        </span>
          <!--标题-->
          <router-link :to="{
        name:'post_content',
        params:{
          id:post.id,
          name:post.author.loginname
        }
        }">
          <span>
          {{ post.title }}
         </span>
          </router-link>
          <!--最終回复时间-->
          <span class="last_reply">
          {{ post.last_reply_at | formatDate }}
        </span>
        </li>
        <li>
          <!--分页-->
          <pagination @handleList="renderList" ref="rest"/>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import pagination from './Pagination'

export default {
  name: 'PostList',
  data() {
    return {
      isLoading: false,
      posts: [],//代表页面的列表数组
      postpage: 1,
      category: '',
    }
  },
  components: {
    pagination
  },
  methods: {
    getData(category = '') {
      this.$http.get('https://cnodejs.org/api/v1/topics', {
        params: {
          page: this.postpage,  //get请求一定要用params传递，post可不用
          limit: 20,
          tab: category
        }
      })
          .then(res => {
            this.isLoading = false //加载成功，去除动画
            this.posts = res.data.data
          })
          .catch(function (err) {
            //处理返回失败后的问题
            console.log(err)
          })
    },
    choseCategory(category = '') {
      this.getData(category)
      this.$refs.rest.changeBtn('reset')
    },
    renderList(value) {
      this.postpage = value
      this.getData(this.category)
    }
  },
  beforeMount() {
    this.isLoading = true //加载成功之前显示加载动画
    this.getData(this.category) //在页面加载之前获取数据
  }
}
</script>

<style scoped>
.PostList {
  background-color: #e1e1e1;
}

.posts {
  margin-top: 10px;
}

.PostList img {
  height: 30px;
  width: 30px;
  vertical-align: middle;
}

ul {
  list-style: none;
  width: 100%;
  max-width: 1344px;
  margin: 0 auto;
}

ul li:not(:first-child) {
  padding: 9px;
  font-size: 15px;
  font-family: "Helvetica Neue", "Luxi Sans", "DejaVu Sans", Tahoma, "Hiragino Sans GB", STHeiti, sans-serif !important;
  font-weight: 400;
  background-color: white;
  color: #333;
  border-top: 1px solid #f0f0f0;
}

li:not(:first-child):hover {
  background: #f5f5f5;;
}

li:last-child:hover {
  background: white;
}

li span {
  line-height: 30px;
}

.allcount {
  width: 80px;
  display: inline-block;
  text-align: center;
  font-size: 12px;
}

.reply_count {
  color: #9e78c0;
  font-size: 14px;
}

.put_good, .put_top {
  background: #80bd01;
  padding: 2px 4px;
  border-radius: 3px;
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
  -o-border-radius: 3px;
  color: #fff;
  font-size: 12px;
  margin-right: 10px;
}

.topiclist-tab {
  background-color: #e5e5e5;
  color: #999;
  padding: 2px 4px;
  border-radius: 3px;
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
  -o-border-radius: 3px;
  font-size: 12px;
  margin-right: 10px;
}

.last_reply {
  text-align: right;
  min-width: 50px;
  display: inline-block;
  white-space: nowrap;
  float: right;
  color: #778087;
  font-size: 12px;
}

.toobar {
  display: flex;
  align-items: center;
  height: 40px;
  background-color: #f5f5f5;
}

.toobar button {
  border: none;
  background: #f5f5f5;
  font-size: 14px;
  color: #80bd01;
  line-height: 30px;
  margin: 0 10px;
  cursor: pointer;
}

.toobar button:hover {
  color: #9e78c0;
}

a {
  text-decoration: none;
  color: black;
}

a:hover {
  text-decoration: underline;
}

.loading {
  text-align: center;
  padding-top: 300px;
}
</style>

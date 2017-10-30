<template>
  <div class="ArticleSection">
    <div class="loading" v-if="loading">
      Loading...
    </div>
    <div class="article" v-else="">
      <h1>{{ post.title }}</h1>
      <ul>
        <li>• 分类：{{ postTab }}</li>
        <li>• {{ post.visit_count }}次访问</li>
        <li>• 发布于：{{ post.create_at | formatDate }}</li>
        <li>• 作者：
          <router-link :to="{ name:'user_info' , params:{ name:post.author.loginname } }">
            {{ post.author.loginname }}
          </router-link>
        </li>
      </ul>
      <div v-html="post.content" id="content"></div>
    </div>
    <div id="reply">
      <div v-for="( reply,index ) in post.replies" class="replySec">
        <div class="replyUp">
          <router-link :to="{ name:'user_info' , params:{ name:reply.author.loginname } }">
            <img :src="reply.author.avatar_url" alt="">
          </router-link>
          <router-link :to="{ name:'user_info' , params:{ name:reply.author.loginname } }">
            {{ reply.author.loginname }}
          </router-link>
          <span>{{ index + 1 }}楼</span>
        </div>
        <section v-if="reply.ups.length > 0" class="thumbsClass">
          <span>❤ </span>{{ reply.ups.length }}
        </section>
        <p v-html="reply.content"></p>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Article",
    data() {
      return {
        post: {
          author: {
            loginname: "temp", // 设置默认值，防止Vue在axios未被调用前报错
          }
        },
        loading: false
      }
    },
    computed: {
      postTab() {
        var tabTxt;
        var type = this.post.tab.toString();

        switch (type) {
          case "ask":
            tabTxt = "问答";
            break;
          case "share":
            tabTxt = "分享";
            break;
          case "job":
            tabTxt = "招聘";
            break;
          case "good":
            tabTxt = "精华";
            break;
        }
        return tabTxt;
      }
    },
    methods: {
      getData() {
        // 获取相关文章信息
        this.$http({
          url: `https://cnodejs.org/api/v1/topic/${this.$route.params.id}`,
          method: "get",
          params: {
            mdrender: true // 当为 false 时，不渲染。默认为 true，渲染出现的所有 markdown 格式文本
          }
        }).then((response) => {
          if (response.data.success === true) {
            this.post = response.data.data;
            this.loading = false;
          }
        }).catch(function (error) {
          console.log(error);
        });
      }
    },
    beforeMount() {
      this.loading = true;
      this.getData();
    }
  }
</script>

<style scoped>
  @import url("../assets/markdown-github.css");

  .ArticleSection {
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
    display: inline-block;
    width: 70%;
    border: 1px solid #ddd;
    padding: 0.8rem 0.4rem;
    margin-left: 3%;
  }

  .ArticleSection #content {
    padding: 2rem 1rem 1rem;
    line-height: 1.6;
  }

  .ArticleSection > h1 {
    font-size: 0.1rem;
  }

  .article {
    background: white;
    margin-bottom: 10px;
    padding-left: 20px;
    padding-top: 10px;

  }

  .article h1 {
    font-size: 1.2rem;
    font-weight: 600;
  }

  .article > ul li {
    display: inline-block;
    font-size: 0.8rem;
    color: #a8a3a3;
    padding-left: 5px;
  }

  .article li a {
    color: inherit;
    text-decoration: none;
  }

  .replySec {
    box-sizing: border-box;
    border-bottom: 1px solid #C0CCDA;
    width: 100%;
    padding: 1rem;
    background: white;
  }

  #reply {
    display: flex;
    flex-direction: column;
  }

  .replyUp {
    display: inline-block;
  }

  .replyUp span {
    display: inline-block;
    vertical-align: top;
    margin-top: 7px;
    margin-left: 6px;
    font-size: 14px;
    color: #806767;
  }

  .thumbsClass {
    float: right;
  }

  .replyUp > a {
    color: black;
    text-decoration: none;
    font-weight: 600;
  }

  .replyUp a:nth-of-type(2) {
    margin-left: -14px;
    vertical-align: super;
  }

  .thumbsClass {
    color: red;
  }

  .replySec > p {
    padding-left: 50px;
    clear: both;
  }

  .replySec img {
    width: 1.5rem;
    height: 1.5rem;
    margin-right: 1rem;
  }

  .replySec p img {
    overflow: hidden;
    width: 100%;
    height: 100%;
  }
</style>

<template>
  <div class="SideBar">
    <div class="loading" v-if="loading">
      Loading...
    </div>
    <div v-else class="authorinfo">
      <div class="authorSummary">
        <p>作者</p>
        <router-link :to="{ name: 'user_info' , params: { name: userinfo.loginname }}">
          <img :src="userinfo.avatar_url"/>
        </router-link>
        <router-link :to="{ name: 'user_info' , params: { name: userinfo.loginname }}" :title="userinfo.loginname">
          {{ userinfo.loginname }}
        </router-link>
        <section>
          积分：{{ userinfo.score }}
        </section>
      </div>
      <div class="recentTopics">
        <p>作者最近主题</p>
        <ul>
          <li v-for="list in topicsLimitBy5">
            <router-link
              :to="{ name: 'post_content' , params: { id: list.id , name:list.author.loginname }}" :title="list.title">
              {{ list.title }}
            </router-link>
          </li>
        </ul>
      </div>
      <div class="recentReplies">
        <p>作者最近回复</p>
        <ul>
          <li v-for="list in userinfo.recent_replies">
            <router-link
              :to="{ name: 'post_content' , params: { id: list.id , name:list.author.loginname }}" :title="list.title">
              {{ list.title }}
            </router-link>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "SideBar",
    data() {
      return {
        userinfo: null,
        loading: false
      }
    },
    methods: {
      getData() {
        // 获取用户信息
        this.$http({
          url: `https://cnodejs.org/api/v1/user/${this.$route.params.name}`,
          method: "get"
        }).then((response) => {
          if (response.data.success === true) {
            this.userinfo = response.data.data;
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
    },
    computed: {
      topicsLimitBy5() {
        return this.userinfo.recent_topics.slice(0, 5);
      }
    }
  }
</script>

<style scoped>
  .SideBar {
    float: right;
    width: 24%;
    box-sizing: border-box;
    margin-right: 3%;
    padding: 0.8rem 0.4rem;
  }

  .SideBar .authorinfo {
    background: white;
  }

  .SideBar > div p {
    padding: 12px 0 12px 12px;
    background-color: rgba(212, 205, 205, 0.17);
    font-size: 0.75rem;
  }

  .SideBar img {
    width: 3.5rem;
    display: inline-block;
    padding: 0 0 0 6px;
    border-radius: 3px;
  }

  .SideBar a {
    text-decoration: none;
    color: #a8a3a3;
  }

  .SideBar .authorSummary > a {
    display: inline-block;
    padding: 12px 0 0 6px;
    vertical-align: top;
  }

  .SideBar .authorSummary > a[title] {
    margin-top: 20px;
    margin-left: 2px;
  }

  .SideBar section {
    padding: 12px 0 12px 12px;
    font-size: 0.75rem;
  }

  .SideBar li {
    list-style: none;
  }

  .recentTopics, .recentReplies {
    font-size: 0.72rem;
    border-top: 10px #DDDDDD solid;
  }

  .recentTopics li,
  .recentReplies li {
    padding: 4px 0 0 12px;
    color: #C0CCDA;
    white-space: nowrap;
  }

  .recentTopics ul,
  .recentReplies ul {
    padding: 10px 0 10px 0;
  }

  .recentTopics li a,
  .recentReplies li a {
    text-overflow: ellipsis;
    overflow: hidden;
    max-width: 95%;
    display: inline-block;
  }
</style>

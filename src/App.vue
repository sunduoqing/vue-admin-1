<template>
  <div id="app">
    <div class="app-header">
      <div class="title">后台管理系统</div>
    </div>
    <div class="app-content">
      <div class="app-nav">
        <app-nav></app-nav>
      </div>
      <div class="app-wrap">
        <!-- 此处放置el-tabs代码 -->
        <div class="template-tabs">
          <el-tabs
            v-model="activeIndex"
            type="border-card"
            closable
            @tab-click="tabClick"
            v-if="options.length"
            @tab-remove="tabRemove">
            <el-tab-pane
              :key="item.name"
              v-for="(item, index) in options"
              :label="item.name"
              :name="item.route">
            </el-tab-pane>
          </el-tabs>
        </div>
        <div class="content-wrap">
          <router-view/>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import AppNav from './components/common/AppNav';
export default {
  name: 'app',
  components: {
    AppNav
  },
  methods: {
    // tab切换时，动态的切换路由
    tabClick (tab) {
      let path = this.activeIndex;
      // 用户详情页的时候，对应了二级路由，需要拼接添加第二级路由
      if (this.activeIndex === '/userInfo') {
          path = this.activeIndex + '/' + this.$store.state.userInfo.name;
      }
      this.$router.push({path: path});
    },
    tabRemove (targetName) {
      // 首页不可删除
      if(targetName == '/') {
        return;
      }
      this.$store.commit('delete_tabs', targetName);
      if (this.activeIndex === targetName) {
        // 设置当前激活的路由
        if (this.options && this.options.length >= 1) {
          this.$store.commit('set_active_index', this.options[this.options.length-1].route);
          this.$router.push({path: this.activeIndex});
        } else {
          this.$router.push({path: '/'});
        }
      }
    }
  },
  computed: {
    options () {
      return this.$store.state.options;
    },
    activeIndex: {
      get () {
        return this.$store.state.activeIndex;
      },
      set (val) {
        this.$store.commit('set_active_index', val);
      }
    }
  },
  watch: {
    '$route'(to) {
      let flag = false;
      for (let option of this.options ) {
        if (option.name === to.name) {
          flag = true;
          this.$store.commit('set_active_index', '/' + to.path.split('/')[1]);
          break
        }
      }
      if (!flag) {
        this.$store.commit('add_tabs', {route: '/' + to.path.split('/')[1], name: to.name});
        this.$store.commit('set_active_index', '/' + to.path.split('/')[1]);
      }
    }
  }
}
</script>

<style lang="scss">

html, body {
  height: 100%;
  margin: 0;
  padding: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  height: 100%;
  display: flex;
  flex-flow: column;
  overflow: hidden;
  .app-header {
    color: #fff;
    flex: 0 0 60px;
    background: #324057;
    height: 60px;
    line-height: 60px;
    padding: 0 20px;
    .title {
      font-size: 24px;
    }
  }
  .app-content {
    flex: 1;
    display: flex;
    flex-flow: row;
    .app-nav {
      flex: 0 0 300px;
      background: #eff2f7;
    }
    .app-wrap {
      flex: 1;
      padding: 10px 20px;
      overflow: auto;
      .content-wrap {
        height: 90%;
        border: 1px solid #d1dbe5;
        border-top: none;
        padding: 0 20px;
      }
    }
  }
}
</style>

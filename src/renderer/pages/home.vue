<style lang='less' scoped>
 .home{
  display: flex;
  display: -webkit-flex;
  height: 100%;
  .left-tabs{
    width: 75px;
    height: 100%;
    background: #EAEAE8;
    padding: 0;
    position: relative;
    top: 0;
    left: 0;
    .avatar{
      display: block;
      width: 50px;
      height: 50px;
      border-radius: 50%;
      margin: 100px auto 40px;
      overflow: hidden;
      img{
        width: 100%;
        height: 100%;
      }
    }
    .tab{
      display: block;
      width: 75px;
      height: 60px;
      text-align: center;
      color: #B6B7B9;
      .active{
        color: #4B4C4F;
      }
    }
  }

  .home-conatiner{
    flex: 1;
    width: 100%;
    height: 100vh;
    word-break:break-all;
  }
  .fixed-xcx-tips{
    position: fixed;
    right: 0;
    bottom: 0;
    width: calc(100% - 75px);
    height: 60px;
    z-index: 999;
    .xcx-tips{
      width: 90%;
      height: 50px;
      border-radius: 5px;
      background: #EAEAE8;
      margin: 0 auto;
      display: flex;
      display: -webkit-flex;
      justify-content: center;
      align-items: center;
      position: relative;
      top: 0;
      left: 0;
      .xcx-img{
        display: none;
        position: absolute;
        right: 0;
        bottom: 50px;
        width: 100%;
        background: #FFF;
        text-align: center;
        padding: 20px 10px;
        box-sizing: border-box;
        img{
          width: 80%;
        }
      }
      .xcx-intro{
        flex: 1;
        text-align: center;
        font-weight: 700;
      }
      .btn{
        display: inline-block;
        padding: 3px 10px;
        margin: 30px;
        border: 1px solid #5AA356;
        color: #5AA356;
        border-radius: 5px;
        cursor: pointer;
        &:hover{
          .xcx-img{
            display: block;
          }
        }
      }
    }
  }
 }
</style>

<template>
  <div class="home" :style="flexDirection">
    <div class="left-tabs drag">
      <div v-if="!$isMac" class="window-options no-drag">
        <Icon custom="window-option iconfont icon-locationfuzhi" size="20" @click="hiddenAppEvent"></Icon>
        <Icon custom="window-option iconfont icon-guanbi1" size="20" @click="closeAppEvent"></Icon>
      </div>
      <div class="avatar no-drag" @click="goPath('/aboutus')">
        <img src="../assets/logo.png">
      </div>
      <div class="tab no-drag" v-for="tab,index in getMenus" :key="index" @click="goPath(tab.path)">
        <Icon :class="{'active':$route.name==tab.name}" :custom="tab.meta.icon" :size="35"></Icon>
      </div>
    </div>
    <div class="home-conatiner">
      <router-view></router-view>
    </div>
    <!-- 悬浮 提示框 -->
    <div v-if="showAD && getAD" class="fixed-xcx-tips">
      <div class="xcx-tips">
        
        <div class="xcx-intro" v-html="getAD.intro">
        </div>
        <div class="btn" @click="redirect">
          <div v-if="getAD.type==='float-tips'" class="xcx-img">
            <img :src="getAD.url">
          </div>
          {{getAD.btn}}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import { MENUS } from '../router/router'
  import { ipcRenderer, shell } from 'electron'
  export default {
    name: 'home',
    data () {
      return {
        showAD: true,
        adIndex: 0,
        adInterval: null,
        ads: [{
          intro: '<p>打开并收藏小程序</p><p>随时随地查看消息</p>',
          type: 'float-tips',
          btn: '查看',
          url: require('@/assets/code.jpg')
        }, {
          intro: '<p>用的爽不爽</p><p>这里来吐槽</p>',
          type: 'redirect-web',
          btn: '去吐槽',
          url: 'https://support.qq.com/embed/phone/111465'
        }]
      }
    },
    components: {
    },
    computed: {
      getAD () {
        return this.ads[this.adIndex]
      },
      getMenus () {
        return MENUS
      },
      flexDirection () {
        return this.$isMac ? {
          flexDirection: 'row'
        } : {
          flexDirection: 'row-reverse'
        }
      }
    },
    mounted () {
      let self = this
      if (!this.adInterval) {
        this.adInterval = setInterval(() => {
          self.adIndex = self.adIndex >= self.ads.length - 1 ? 0 : ++self.adIndex
        }, 5000)
      }
    },
    methods: {
      goPath (path) {
        if (this.$route.path === path) return
        this.$router.push(path)
      },
      hiddenAppEvent () {
        ipcRenderer.send('hiddenApp')
      },
      closeAppEvent () {
        ipcRenderer.send('closeApp')
      },
      redirect () {
        const enableTypes = ['redirect-web', 'redirect-router']
        if (!this.ad.type || enableTypes.indexOf(this.ad.type) === -1 || !this.ad.url) return
        shell.openExternal(this.ad.url)
      }
    }
  }
</script>


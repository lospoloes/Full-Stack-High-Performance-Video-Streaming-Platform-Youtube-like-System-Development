<script>
import userUtils from "@/utils/userUtils";
import LoginDialog from "@/components/LoginDialog.vue";
import userMomentApi from "@/api/userMomentApi";
import userHistoryApi from "@/api/userHistoryApi";

export default {
  name: "CommonHeader",
  components: {LoginDialog},
  data(){
    return {
      dialogVisible:false,
      entries:[
        {
         id:1,
         name:'Homepage',
         path:'/'
        },
        {
          id:2,
          name:'Anime',
          path:'/'
        },
        {
          id:3,
          name:'Live Stream',
          path:'/'
        },
        {
          id:4,
          name:'Game Center',
          path:'/'
        }
      ],
      searchTxt:'',
      moments:[],
      histories:[]
    }
  },
  mixins:[userUtils],
  methods:{
    jumpWithLoginUser(path){
      //先判断用户是否登录，如果没有登录，弹出用户登录对话框
      if(!this.isUserLoggedIn){
        this.dialogVisible = true;
      }else{
        //打开一个新的页面地址
        //如果当前页面的路径等于我们要跳转的路径，则只需要刷新页面
        if(this.$route.path !== path){
          this.$router.push(path);
        }else{
          location.reload();
        }
      }
    },

    jumpToPath(path){
      if(this.$route.path !== path){
        this.$router.push(path);
      }else{
        location.reload();
      }
    },

    searchContents(){
      if(this.searchTxt === ''){
        window.alert('Please enter search content');
        return;
      }
      //判断是否重复跳转
      if(decodeURIComponent(this.$route.fullPath)
          === '/searchContents?searchTxt='+this.searchTxt){
        location.reload();
        return;
      }
      this.$router.push({
        path:'/searchContents',
        query:{
          searchTxt:this.searchTxt
        }
      })
    }

  },

  async mounted() {
    if(this.isUserLoggedIn){
      this.$store.state.userInfo = await this.getUserBasicInfo();

      let params = {
        size:5,
        no:1
      }
      //查询动态
      let response = await userMomentApi.pageListMoments(params);
      if(response.data){
        this.moments = response.data.list;
      }
      //查询历史
      let response1 = await userHistoryApi.pagListUserVideoHistory(params);
      if(response1.data){
        this.histories = response1.data.list;
      }
    }

  },

  computed:{

    // isUserLoggedIn(){
    //   return localStorage.getItem('token');
    // }
    avatar(){
      const userInfo = this.$store.state.userInfo;
      if(userInfo && userInfo.avatar && userInfo.avatar !== '' ){
        return userInfo.avatar;
      }else{
        return require('@/assets/bilibiliavatar.png');
      }
    }

  },
}
</script>

<template>

  <div class="bili-header">

    <div class="header-nav-container">

      <div class="left-entry">
        <div v-for="entry in entries" :key="entry.id">
          <span @click="jumpToPath(entry.path)">{{entry.name}}</span>
        </div>
      </div>

      <div class="center-search-bar">
        <form class="nav-search-form">
          <div class="nav-search-content">
            <input class="nav-search-input"
                    type="text" autocomplete="off"
                    maxlength="100" placeholder="Please enter search contents..."
                    v-model="searchTxt">
          </div>
          <div class="nav-search-btn">
            <el-button icon="el-icon-search"
                       circle size="mini" @click="searchContents"></el-button>
          </div>
        </form>

      </div>

      <div class="right-entry">

        <div v-if="isUserLoggedIn" class="user-center">
          <el-dropdown>
            <span class="el-dropdown-link">
              <img style="height: 50px; width: 50px; border-radius: 50%"
                   :src="avatar" alt="">
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>
                <el-button type="text" style="color: black"
                           @click="jumpToPath('/mySpace')">
                  Personal Center
                </el-button>
              </el-dropdown-item>
              <el-dropdown-item style="color: black">
                <el-button type="text" style="color: black"
                           @click="logout">
                  Logout
                </el-button>
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
        <div v-else class="user-center">
          <el-button class="login-button" type="text" @click="dialogVisible = true">
            Login
          </el-button>
          <el-dialog
              title="Password login"
              :visible.sync="dialogVisible"
              width="30%">
            <LoginDialog/>
          </el-dialog>
        </div>
<!--        动态-->
        <div class="right-entry-moments">
          <el-popover
              placement="top-start"
              width="300"
              trigger="hover">
            <div v-if="isUserLoggedIn">
<!--              <div>-->
<!--                这里是动态内容-->
<!--              </div>-->
<!--              <el-button type="info">-->
<!--                查看更多-->
<!--              </el-button>-->

              <div class="moment-list">
                <div class="moment-list-item" v-for="moment in moments" :key="moment.id"
                     style="background-color: #f1f1f1; margin-bottom: 10px; border-radius: 5px; padding: 5px">
                  <div class="moment-list-item-txt">
                    {{moment.content.contentDetail.txt}}
                  </div>
                  <div class="moment-list-item-img" v-if="moment.type==='1' " >
                    <img :src="moment.content.contentDetail.img" alt=""
                         style="height: 60px; width: 100px; border-radius: 5px; margin-right: 5px">
                  </div>
                  <div class="moment-list-item-video" v-if="moment.type==='0' " style="display: flex">
                    <img :src="moment.content.contentDetail.thumbnail" alt=""
                         style="height: 60px; width: 100px; border-radius: 5px; margin-right: 5px">
                    <div class="moment-list-item-video-detail" style="display: flex; flex-direction: column; justify-content: space-between">
                      <div class="moment-list-item-video-detail-title">
                        {{moment.content.contentDetail.title}}
                      </div>
                      <div class="moment-list-item-video-detail-description">
                        {{moment.content.contentDetail.description}}
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              <el-button type="info" style="width: 100%" v-if="isUserLoggedIn"
                         @click="jumpToPath('/userMoments')">
                View More
              </el-button>
            </div>

<!--            <div v-else>-->
<!--              <div>-->
<!--                登录即可查看动态-->
<!--              </div>-->
<!--              <el-button type="info">-->
<!--                立即登录-->
<!--              </el-button>-->
<!--            </div>-->

            <div v-else>
              <div style="text-align: center; font-size: 16px; padding: 10px">
                Login to view moments
              </div>
              <el-button type="info" style="width: 100%" @click="dialogVisible=true">
                Login now
              </el-button>
            </div>

<!--            <el-button slot="reference" class="el-btn-moments">hover 激活</el-button>-->
            <el-button slot="reference"
                       icon="el-icon-star-off"
                       class="el-btn-moments"
                       type="warning"
                       circle>
            </el-button>
          </el-popover>
          <span style="color: white; margin-top: 5px">Moments</span>
        </div>
<!--        历史-->
        <div class="right-entry-content">
          <el-popover
              placement="top-start"
              width="300"
              trigger="hover">
            <div class="history-list">
              <div class="history-list-item" v-for="(history,index) in histories"
                   :key="index"
                   style="background-color: #f1f1f1; margin-bottom: 10px; border-radius: 5px; padding: 5px">
                <div class="history-list-item-video" style="display: flex">
                  <img :src="history.thumbnail" alt=""
                       style="height: 60px; width: 100px; border-radius: 5px; margin-right: 5px">
                  <div class="history-list-item-video-detail"
                       style="display: flex; flex-direction: column; justify-content: space-between">
                    <div class="history-list-item-video-detail-title">
                      {{history.title}}
                    </div>
                    <div class="history-list-item-video-detail-description">
                      {{history.description}}
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <el-button type="info" style="width: 100%" v-if="isUserLoggedIn"
                       @click="jumpToPath('/userHistory')">
              View More
            </el-button>
            <div v-else>
              <div style="text-align: center; font-size: 16px; padding: 10px">
                Login to view history
              </div>
              <el-button type="info" style="width: 100%" @click="dialogVisible=true">
                Login now
              </el-button>
            </div>
            <el-button class="right-entry-button" type="warning"
                       slot="reference"
                       icon="el-icon-star-off"
                       @click="jumpWithLoginUser('/userHistory')"
                       circle>
            </el-button>
          </el-popover>
          <span style="color: white; margin-top: 5px">History</span>
        </div>
        <div class="right-entry-content">
          <el-button type="warning" icon="el-icon-upload2" circle
                     @click="jumpWithLoginUser('postContent')"></el-button>
          <span style="color: white; margin-top: 5px">Post</span>
        </div>
      </div>

    </div>

    <div class="header-banner-container">
      <img class="banner"
          :src="require('@/assets/header/header-banner.png')" alt="">
    </div>

  </div>

</template>

<style scoped lang="less">

.bili-header{

  .header-nav-container{

    position: absolute;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;

    .left-entry{
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-left: 40px;
      span{
        margin-right: 20px;
        color: white;
        font-weight: bolder;
        cursor: pointer;
      }
    }

    .center-search-bar{
      flex: 1 auto;
      min-width: 181px;
      max-width: 500px;
      .nav-search-form{
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0 4px 0 4px;
        height: 40px;
        background-color: white;
        border-radius: 8px;
        opacity: 0.8;
        .nav-search-content{
          display: flex;
          align-items: center;
          justify-content: space-between;
          width: 100%;
          height: 36px;
          border: 2px solid transparent;
          border-radius: 6px;
          .nav-search-input{
            width: 100%;
            font-size: 14px;
            padding-right: 8px;
            background-color: transparent;
            border: none;
            box-shadow: none;
            outline: none;
          }
        }

        .nav-search-btn{
          margin: 0;
          padding: 0;
          width: 32px;
          height: 32px;
          border: none;
          border-radius: 6px;
          cursor: pointer;
        }


      }

    }

    .right-entry{
      display: flex;
      flex-direction: row;
      align-items: center;
      padding: 10px;

      .user-center{
        margin-right: 10px;
        .login-button{
          color: #00a1d6;
          width: 50px;
          height: 50px;
          border-radius: 50%;
          font-weight: bold;
          background-color: #fff;
        }
      }
      .right-entry-moments{
        //margin-right: 10px;
        //
        //.el-btn-moments{
        //  height: 100%;
        //}
        margin: 0 10px;
        display: flex;
        flex-direction: column;
        align-items: center;
      }
      .right-entry-content{
        margin: 0 10px;
        //margin-right: 10px;
        display: flex;
        flex-direction: column;
        align-items: center;
      }
    }

  }

  .header-banner-container{
    .banner{
      width: 100%;
    }
  }

}

</style>
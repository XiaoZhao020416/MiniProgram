<template>
  <view class="login-container">
    <!-- 提示登录的图标 -->
    <uni-icons type="contact-filled" size="100" color="#AFAFAF"></uni-icons>
    <!-- 登录按钮 -->
    <!-- 可以从 @getUserProfile 事件处理函数的形参中，获取到用户的基本信息 -->
    <button type="primary" class="btn-login" @click="getUserProfile">一键登录</button>
    <!-- 登录提示 -->
    <view class="tips-text">登录后尽享更多权益</view>
  </view>
</template>

<script>
  // 按需导入辅助函数
  import { mapMutations, mapState } from 'vuex'
  export default {
    name:"my-login",
    methods: {
      // 调用 mapMutations 辅助方法，把 m_user 模块中的方法映射到当前组件中使用
      ...mapMutations('m_user', ['updateUserInfo', 'updateToken', 'updateRedirectInfo']),
      // 获取微信用户的基本信息
      getUserProfile() {
        uni.getUserProfile({
          // 声明获取用户个人信息后的用途，后续会展示在弹窗中，请谨慎填写
          desc: '请求授权您的用户信息', 
          lang: 'zh_CN',
          success: (info) => {
            // 这部分是更新用户信息的
            // 将用户的基本信息存储到 vuex 中
            this.updateUserInfo(info.userInfo)
            // 获取登录成功后的 Token 字符串
            this.getToken(info)
          },
          fail: (res) => {
            return uni.$showMsg("已取消登录授权！")
          }
        })
      },
      // 返回登录之前的页面
      navigateBack() {
        // redirectInfo 不为 null，并且导航方式为 switchTab
        if (this.redirectInfo && this.redirectInfo.openType === 'switchTab') {
          // 调用小程序提供的 uni.switchTab() API 进行页面的导航
          uni.switchTab({
            // 要导航到的页面地址
            url: this.redirectInfo.from,
            // 导航成功之后，把 vuex 中的 redirectInfo 对象重置为 null
            complete: () => {
              this.updateRedirectInfo(null)
            }
          })
        }
      },
      getToken(info) {    
        uni.login({
          provider: 'weixin',
          success: (res) => {
            if (res && res.errMsg === 'login:ok') {
              const query = {
                code: res.code,
                encryptedData: info.encryptedData,
                iv: info.iv,
                rawData: info.rawData,
                signature: info.signature
              }
              //获取token失败时候使用
              const loginResult = {
                "message": {
                  "user_id": 12,
                  "user_email_code": null,
                  "is_active": null,
                  "user_sex": "男",
                  "user_qq": "",
                  "user_tel": "",
                  "user_xueli": "本科",
                  "user_hobby": "",
                  "user_introduce": null,
                  "create_time": 1525402223,
                  "update_time": 1525402223,
                  "token": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjIzLCJpYXQiOjE1NjQ3MzAwNzksImV4cCI6MTAwMTU2NDczMDA3OH0.YPt-XeLnjV-_1ITaXGY2FhxmCe4NvXuRnRB8OMCfnPo"
                },
                "meta": {
                  "msg": "登录成功",
                  "status": 200
                }
              }
              console.log(loginResult);
              if (loginResult.meta.status !== 200) return uni.$showMsg('登录失败！')
              this.updateToken(loginResult.message.token)
              uni.$showMsg('登录成功！')
              // 判断 vuex 中的 redirectInfo 是否为 null
              // 如果不为 null，则登录成功之后，需要重新导航到对应的页面
              this.navigateBack() 
            }
            else {
              uni.$showMsg('登录失败!')
            }
          },
          fail: (err) => {
            uni.$showMsg('登录失败!')
          }
        })
      }
    },
    data() {
      return {
        
      };
    },
    computed: {
      // 调用 mapState 辅助方法，把 m_user 模块中的数据映射到当前用组件中使用
      ...mapState('m_user', ['redirectInfo']),
    },
  }
</script>

<style lang="scss">
  .login-container {
    // 登录盒子的样式
    height: 750rpx;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background-color: #f8f8f8;
    position: relative;
    overflow: hidden;
  
    // 绘制登录盒子底部的半椭圆造型
    &::after {
      content: ' ';
      display: block;
      position: absolute;
      width: 100%;
      height: 40px;
      left: 0;
      bottom: 0;
      background-color: white;
      border-radius: 100%;
      transform: translateY(50%);
    }
  
    // 登录按钮的样式
    .btn-login {
      width: 90%;
      border-radius: 100px;
      margin: 15px 0;
      background-color: #f8e343;
    }
  
    // 按钮下方提示消息的样式
    .tips-text {
      font-size: 12px;
      color: gray;
    }
  }
</style>
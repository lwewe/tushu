<template>
  <div class="conPage">
    <NProgress v-if="loadingflag" />
    <!--  头部-->
    <div class="topBox">
      <div class="leftBox">
        <div class="avatar">
          <van-uploader :after-read="onRead">
            <img class="img" :src="userInfo.img" alt="">
          </van-uploader>
        </div>
        <div>
          <div class="phone">{{ userInfo.phone }}</div>
        </div>
      </div>
      <!-- <div class="settingBox">
        <div class="setting" @click="toStting('/setting')">
          <img class="img" src="@/assets/mine/sz.png" alt="">
        </div>
        <div class="setting" @click="scanQRCode">
          <img class="img" src="@/assets/mine/sys.png" alt="">
        </div>
      </div> -->
    </div>
    <div class="centerBox">
      <div class="freeaBox">
        <div class="coupons1">
          <div class="couponsTitle" @click="scanQRCode" style="display: flex;align-items: center;">扫码绑定
            <img class="img" src="@/assets/mine/sys.png" style="width: 20px;margin-left: 6px;" alt="">
          </div>
          <div class="couponsBox">
            <div class="welfareBox" style="width: 41%;" @click="toFreea">
              <div>
                <div class="welfareCard" style="white-space: nowrap">福利卡</div>
                <div class="none" style="text-align: center;white-space: nowrap;" v-if="userInfo.card_num != 0">{{
                  userInfo.card_num }}张福利卡</div>
                <div class="none" style="text-align: center;white-space: nowrap;" v-else>暂无福利卡</div>
              </div>
              <div class="welfare" style="width: 39px;" >
                <img class="img" src="../../assets/mine/ka.png" alt="">
              </div>
            </div>
            <div class="line"></div>
            <a :href="kefu">
              <div class="welfareBox">
                <div>
                  <div class="welfareCard">在线客服</div>
                  <div class="none">全年无休(9:00-21:00)</div>
                </div>
                <div class="welfare">
                  <img class="img" src="../../assets/mine/kf1.png" alt="">
                </div>
              </div>
            </a>

          </div>
        </div>
        <!-- <div class="ImgBox"@click="toFreea">
          <img class="img" src="../../assets/mine/flk.png" alt="">
        </div>
        <div class="leftBox">
          <div class="freeaText">我的福利卡</div>
        </div>
        <div>
          <van-icon color="#7193D4" name="arrow"/>
        </div> -->
      </div>
      <div class="orderInfo">
        <!--      我的订单-->

        <div class="coupons orderMax allOrder">
          <!--        状态-->
          <div class="couponsTitle">我的订单</div>
          <div class="obligationMax">

            <div class="obligationBox" v-for="item in orderIcon" :key="item.id" @click="toOrder(item.id)">
              <div class="obligationImg">
                <img class="img" :src="item.icon" alt="">
                <div class="point" v-if="item.type && item.id == 1"></div>
                <div class="num" v-if="item.type && item.id == 2">{{ item.type }}</div>
              </div>
              <div class="obligationText">{{ item.text }}</div>
            </div>
          </div>
        </div>
        <!--  功能-->
        <div class="coupons orderMax" style="margin-top: 8px;border-radius: 8px">
          <div class="obligationMax mt5">
            <div class="obligationBox" style=" " v-for="item in mineFun" :key="item.id"
              @click="toFun(item.id, item.path)">
              <div class="obligationImg" style="width: 27px;height:27px; ">
                <img class="img" :src="item.icon" alt="">
              </div>
              <div class="obligationText" style="white-space: nowrap;" :style="{ marginLeft: item.id == 4 ? '0px' : '0px' }">
                {{ item.text }}
              </div>
            </div>
          </div>
        </div>
      </div>



      <!--  -->
      <div>


        <div class="hotmember">好书推荐<span></span> </div>
        <div class="listMax" :style="{ minHeight: isHeight ? 'calc(100vh - 110px)' : '' }"
          :class="{ fixedHeight: show }">
          <div class="listBox">
            <NewShopList :flag="1" :shopSwiper="shopSwiper" :shopList="shopList" v-if="shopList.length > 0"
              :active="active" :swiperHeight="swiperHeight" @input="input"></NewShopList>
            <!--        </div>-->
            <loading style="margin-top: 2px" v-if="isLoading"></loading>
          </div>
        </div>
      </div>


      <!--  -->
    </div>
    <NavigationTab :active="4"></NavigationTab>
  </div>
</template>

<script>
import NavigationTab from "@/components/NavigationTab.vue";
import { bindingcard, changeAvatar, getUserInfo, qrcodeBinging } from "@/api/mine";
import NewShopList from "@/components/NewShopList.vue";

import { getIndexBookList } from "@/api";
import wx from "weixin-js-sdk";

export default {
  name: "PersonCenter",
  components: { NavigationTab, NewShopList },
  data() {
    return {
      active: 0, swiperHeight: 0, shopSwiper: [], shopList: [], isHeight: false, show: false,
      orderIcon: [
        {
          id: 1,
          icon: require("../../assets/mine/add/all.png"),
          text: "全部订单"
        },
        {
          id: 2,
          icon: require("../../assets/mine/add/dsy.png"),
          text: "待收货"
        },
        {
          id: 3,
          icon: require("../../assets/mine/add/dpj.png"),
          text: "已完成"
        },
        {
          id: 4,
          icon: require("../../assets/mine/add/sh.png"),
          text: "退款售后"
        },
      ],
      mineFun: [
        {
          id: 1,
          icon: require("../../assets/mine/add/map.png"),
          text: "地址管理",
          path: "/cakeAddressList"
        },
        {
          id: 2,
          icon: require("../../assets/mine/add/zfmm.png"),
          text: "支付密码"
        },
        {
          id: 3,
          icon: require("../../assets/mine/add/zn.png"),
          text: "使用指南",
        },
        {
          id: 5,
          icon: require("../../assets/mine/add/yj.png"),
          text: "意见反馈",
        },
        {
          id: 4,
          icon: require("../../assets/mine/add/tc.png"),
          text: "退出"
        },
      ],
      userInfo: {},
      token: "",
      kefu: "",
      info: {},
      loadingflag: true,
      isLoading: false,
      pageno: 1,
      isScroll: false,
      // isTab: false,
    }
  },
  methods: {
    input(e) {

    },
    getHot(type = 0, pageno = this.pageno) {
      // if (this.isTab) {
      //   console.log(this.isTab + 'istab')
      //   this.shopList = []
      // }
      this.isLoading = true
      getIndexBookList({
        type,
        pageno,
        pagesize: 10
      }).then(res => {
        this.isLoading = false
        setTimeout(() => {
          this.loadingflag = false
        }, 500)
        if (res.code == 200) {

          if (res.data.book_list.length == 0) {
            this.isScroll = true
            return
          }
          res.data.book_list.forEach(item => {
            this.shopList.push(item)
          })

          // this.isTab = false

          //  console.log(this.isTab + '--istab')

        }
      })
    },
    scanQRCode() {
      let _that = this;
      wx.ready(function () {
        wx.checkJsApi({
          jsApiList: ['scanQRCode'],
          success: function (res) {
            if (res.checkResult.scanQRCode === true) {
              wx.scanQRCode({ // 微信扫一扫接口
                needResult: 1, // 默认为0，扫描结果由微信处理，1则直接返回扫描结果，
                scanType: ['qrCode', 'barCode'], // 可以指定扫二维码还是一维码，默认二者都有
                success: function (res) {
                  let url = res.resultStr // 当needResult 为 1 时，扫码返回的结果
                  let token = localStorage.getItem("token")
                  qrcodeBinging({
                    token,
                    content: url
                  }).then(res => {
                    if (res.code == 200) {
                      setTimeout(() => {
                        _that.$router.go(-1)
                        _that.$toast(res.data)
                      }, 1000)
                    } else {
                      _that.$toast(res.msg)
                    }
                  })
                }
              })
            } else {
              _that.$toast("抱歉，当前客户端版本不支持扫一扫");
            }
          },
          fail: function (res) { // 检测getNetworkType该功能失败时处理
            _that.$toast('fail' + res)
          }
        });
      });
      //错误信息
      wx.error(function (res) {
        _that.$toast("出错了：" + res.errMsg);
      });
    },
    getSign() {
      // this.$toast({message:purl,duration:0})
      //请求后端，获取微信签名信息
      let configData = {
        debug: false,
        appId: this.info.appid, // 必填，公众号的唯一标识
        timestamp: "" + this.info.time, // 必填，生成签名的时间戳
        nonceStr: this.info.nonceStr, // 必填，生成签名的随机串
        signature: this.info.signature,// 必填，签名
        jsApiList: ['scanQRCode', 'checkJsApi']
      }
      wx.config(configData);
      wx.ready(function (res) {

      });
      wx.error(function (res) {
      });
    },
    getbindingcard() {
      let purl = /(Android)/i.test(navigator.userAgent) ? location.href.split('#')[0] : window.localStorage.getItem('scanUrl');
      bindingcard({
        url: purl
      }).then(res => {
        this.info = res.data
        this.getSign()
      })
    },
    // 上传头像
    onRead(file) {
      var formdata = new FormData(); //new一个FormData
      formdata.append('img', file.file)  //把上传的图片信息放入，传给后台
      formdata.append('token', this.token)  //把上传的图片信息放入，传给后台
      formdata.append('channel_no', "12358")  //把上传的图片信息放入，传给后台
      changeAvatar(formdata).then(res => {
        if (res.code == 200) {
          this.userInfo.img = res.data
          this.$toast("修改头像成功")
        }
      })
    },
    toOrder(id) {
      if (id == 4) {
        window.location.href = this.kefu
      } else {
        this.$router.push({ path: "/order", query: { tabIndex: id } })
      }

    },
    //   去福利卡
    toFreea() {
      window.location.href = this.$store.state.base + "/securitycards?change=3&token=" + localStorage.getItem("token") + "&cityName=" + sessionStorage.getItem("cityName")
    },
    //   去支付密码
    toFun(id, path) {
      if (id == 2) {
        window.location.href = this.$store.state.base + "/payPassword" + "?token=" + localStorage.getItem("token") + "&change=3"
      } else if (id == 3) {
        window.location.href = this.$store.state.Shopbase + "/userGuide"
      } else if (id == 5) {
        window.location.href = this.$store.state.base + "/feedback"
      } 
      else if (id == 4) {
        // window.location.href = this.kefu
        this.$dialog.confirm({
          title: '退出',
          message: '是否退出登录？',
          confirmButtonColor: 'red'
        }).then(() => {
          localStorage.removeItem("token")
          localStorage.removeItem("uid")
          this.$store.commit("settoken", "")
          this.$toast("退出登录成功")
          setTimeout(() => {
            this.$router.replace({ path: "/index" })
          }, 1000)
        }).catch(() => {
        })
      } else {
        this.$router.push(path)
      }
    },
    toStting(path) {
      this.$router.push(path)
    },
    getUserInfo() {
      getUserInfo().then(res => {
        this.loadingflag = false
        if (res.code == 200) {
          this.userInfo = res.data
        }
      })
    },

    handleScroll(e) {

      let scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
      let scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight;
      let clientHeight = document.documentElement.clientHeight || document.body.clientHeight;
      console.log(scrollTop)
      console.log(scrollHeight)
      console.log(clientHeight)


      if (Math.ceil(scrollTop + clientHeight) >= scrollHeight) {
        this.onMost()
      }
    },
    onMost() {
      if (!this.isScroll) {
        this.pageno++
        this.getHot()
      }
    },



  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll);
  },
  created() {
    this.token = localStorage.getItem("token")
    this.getUserInfo()
    this.kefu = localStorage.getItem("kefu")
    // this.$store.commit("changeFilm", this.$route.query.film)
    // console.log(this.$store.state.film)
    this.getbindingcard()
    this.getHot()
  }
}
</script>

<style scoped lang="less">
.conPage {
  background-image: linear-gradient(to bottom, #EAEDEF, #F2F2F2);
  min-height: calc(100vh - 50px);
  box-sizing: border-box;
}

.topBox {
  background-image: url("@/assets/mine/dback.png");
  width: 100%;
  height: 217px;
  background-size: 100% 100%;
  box-sizing: border-box;
  padding: 8px 10px 0px 10px;
  display: flex;
  align-items: flex-start;
  justify-content: space-between;

  .avatar {
    width: 60px;
    height: 60px;
    border-radius: 50%;
    overflow: hidden;
  }

  .leftBox {
    display: flex;
    align-items: center;
    gap: 10px;
    padding-top: 14px;

    .phone {
      font-weight: bold;
      margin-top: 5px;
      padding-left: 7px;
    }
  }

  .settingBox {
    display: flex;
    align-items: center;
    gap: 15px;
    margin-top: 5px;

    .setting {
      width: 24px;
    }
  }
}

.centerBox {
  padding: 0px 15px;
  margin-top: -116px;
  padding-bottom: 100px;

  .ImgBox {
    width: 31px;
  }

  .freeaBox {
    margin-bottom: 10px;
    // display: flex;
    // align-items: center;
    background-color: #ffffff;
    // background-image: url("@/assets/mine/xback.png");
    width: 100%;
    height: 100px;
    // background-size: 100% 100%;
    box-sizing: border-box;
    padding: 0px 10px;
    // gap: 6px;
    border-radius: 8px;

    .vipIcon {
      width: 32px;
    }

    .leftBox {
      display: flex;
      align-items: center;
      gap: 5px;
      font-size: 19px;
      //color: #6487C7;
      font-weight: bold;
      //font-style: italic;
      background-image: linear-gradient(to bottom, #A0BAEE, #688AC9);
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
    }

    .freeaText {
      margin-top: -5px;
    }
  }

  .coupons {
    background-color: white;
    padding: 10px 10px;
    border-radius: 8px;

    .couponsTitle {
      font-weight: bold;
      padding-bottom: 10px;
      color: #343434;
    }
  }

  .allOrder {
    box-shadow: 0px 0px 3px 1px #E7EFFA;
  }

  //我的订单
  .orderMax {

    .arrow {
      height: 11px;
      padding-left: 3px;
    }

    .obligationBox {
      text-align: center;
    }

    .obligationImg {
      width: 28px;
      height: 28px;
      margin: auto;
      position: relative;
    }

    .obligationText {
      // margin-top: 8px;
      font-size: 13px;
      font-weight: bold;
    }

    .point {
      position: absolute;
      width: 8px;
      height: 8px;
      background-color: #ED3036;
      border-radius: 50%;
      top: 0px;
      right: 0px;
    }

    .num {
      position: absolute;
      min-width: 13px;
      height: 10px;
      border-radius: 50%;
      top: -10px;
      left: 17px;
      border: 1px solid #ed3036;
      color: #ed3036;
      font-size: 10px;
      padding: 1px 1px 3px;
    }
  }

  .obligationMax {
    display: flex;
    align-items: center;
    justify-content: space-between;
    // padding: 0px 0px 0px;
    padding: 0 10px;
  }

  .obligationMax.mt5 {
    padding: 8px;

    .obligationText {
      margin-top: 3px;
    }
  }
}

.hotmember {
  margin-top: 10px;
  background: #ffffff;
  border-radius: 8px;
  padding: 8px 15px;
  position: relative;
  font-weight: bold;
  color: #383838;

  span {
    position: absolute;
    bottom: 0;
    left: 32px;
    background-color: #4BB28D;
    height: 4px;
    width: 30px;
    border-radius: 4px;
    display: inline-block;
  }
}

.orderInfo {
  background-size: 100% auto;
  // padding: 0px 10px;
  box-sizing: border-box;
  width: 100%;
  // margin-left: -16px;
  // margin-top: -4px;
  position: relative;
  z-index: 2;
}

// .line {
//   position: absolute;
//   right: -18px;
//   top: 3px;
//   width: 8px;
// }

//我的卡券
.coupons1 {


  .couponsTitle {
    font-weight: bold;
    color: #383838;
    padding-top: 10px;
  }

  .welfare {
    min-width: 43px;
    width: 43px;
  }

  .welfareBox {
    display: flex;
    align-items: center;
    justify-content: space-between;
    //width: 42%;
    padding: 4px 10px 0px 0;
    gap: 5px;
  }

  .welfareBox:last-child {
    display: flex;
    align-items: center;
    justify-content: space-between;
    //width: 42%;
    padding: 4px 0px 0px 10px;
    gap: 5px;
  }

  .none {
    font-size: 12px;
    color: #696969;
    margin-top: 5px;
  }

  .welfareCard {
    font-weight: bold;
    font-size: 15px;
    color: #343434;
    white-space: nowrap;
  }

  .couponsBox {
    display: flex;
    align-items: center;
    padding-top: 8px;
    //justify-content: space-between;
  }

  .line {
    width: 1px;
    background-color: #FAF5F5;
    height: 45px;
  }
}
</style>
<template>
    <div class="postStation" >
      <img class="logo" :src="logo">
      <img  class="btnSearchReward" :src="btnSearchReward" @click="searchReward()" >
      <img  class="btnBack":src="btnBack" @click="back()" >
      <img class="bgImg" :src="bgImg" >
      <img class="flowerpot" :src="flowerpot">
      <div class="mailbox" :style="mailboxStyle">
      </div>
      <div class="textMailboxWrap">
        <div class="text">您可寄出的明信片 <span class="blueNumber">{{cardCount}}</span> 张，选择一张点击查看并寄出吧！</div>
        <!--<img  class="storage" :src="storage" >-->
        <div class="storage" :style="storageStyle">
          <div class="cards" >
            <div class="cardiv" v-for="(item,index) in cards"  :item="item" :key="index">
              <img
                :src="item.url"
                @click="showBigPic(item.url,item.pid)"
              >
            </div>
          </div>
        </div>
      </div>
      <!--点击明信片放大查看-->
      <div class="showBigImgBox"  v-show="bigImgShow">
          <div class="bigImgBox">
            <img class="bigImg" :src="bigImgUrl" >
            <img class='closeBtn' :src="closeBtn" @click="closeBigImgBox">
            <img  class="postBtn" :src="postBtn" @click="postCardOut(postOutPId)">
            <img  class="postCancelBtn" :src="postCancelBtn"  @click="closeBigImgBox">
          </div>
      </div>
      <!--完成邮寄弹窗-->
      <div class="getBackLetter" v-show="completePost">
        <div class="backLetterWrap">
          <img  class="backLetter" :src="backLetter" >
          <img  class="btnckLetter" :src="btnckLetter" @click="toReward()" >
        </div>
      </div>
      <!--查看中奖结果-->
      <div class="rewardResult" v-show="showRewardResult">
        <div class="resultBoxWrap">
          <img class='closeBtn' :src="closeBtn" @click="closeRewardResult">
          <img  class="resultTitle" :src="resultTitle" alt="">
          <div class="resultBox">
            <div  class="rewardContainer">
              <img  class='noGetReward' :src="noGetReward"  v-if="showNoReward">
              <div class="resultHotel" v-if="rewardResultHotel.length>0">
                <img class="rewardResutTitle" :src="hotelTitle" >
                <div class="hotelCardwrap"   v-for="(item, index) in rewardResultHotel" :item="item" :key="index">
                  <img class="hotelCard" :src="hotelcard">
                  <div class="changeInfo">
                    <div class="code">兑奖码：{{ item.code}}</div>
                    <!--<div class="valueDate">有效期：{{item.valueDate}}</div>-->
                  </div>
                </div>
                <div class="hotelText">中奖者请凭兑奖码在有效期前，提前三天致电023-63718080/13101342268预约兑换奖品，过期视为自动放弃奖品。</div>
              </div>
              <div class="resultCrash" v-if="rewardResultCrash.length>0">
                <img class="rewardResutTitle" :src="crashTitle" >
                <div class="crashCardwrap">
                  <img class="crashCard" :src="crashcard">
                  <div class="textInfo">{{rewardResultCrash[0].count }}</div>
                </div>
                <div class="crashText">请注意查看服务通知消息并领取红包。</div>
              </div>
              <div class="resultPlace" v-if="rewardResultInterst.length>0">
                <img class="rewardResutTitle" :src="interestTitle" >
                <div class="interestCardwrap"   v-for="(item, index) in rewardResultInterst" :item="item" :key="index">
                  <img  class="placecard" :src="placecard" >
                  <div class="changeInfo">
                    <div class="code">兑奖码：{{ item.code}}</div>
                    <!--<div class="valueDate">有效期：{{item.valueDate}}</div>-->
                  </div>
                </div>
                <div class="interestText">中奖者请凭兑奖码在有效期前，提前三天致电023-63718080/13101342268预约兑换奖品，过期视为自动放弃奖品。</div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <audio  id="btnMusic1">
        <source :src="btnMusic" type="audio/ogg" >
      </audio>
    </div>
</template>

<script>
  import logo from '../assets/images/logored.png'
  import btnSearchReward from '../assets/images/btnSearchReward.png'
  import btnBack from '../assets/images/btnback.png'
  import bgImg from '../assets/images/pbg4.jpg'
  import flowerpot from '../assets/images/flowerpot.png'
  import  mailbox from '../assets/images/mailbox.png'
  import  storage from '../assets/images/storage.png'
  import  closeBtn from '../assets/images/closeBtn.png'
  import noGetReward from '../assets/images/noGetReward.png'
  import resultTitle from '../assets/images/resultTitle.png'
  import hotelTitle from '../assets/images/hotelTitle.png'
  import interestTitle from '../assets/images/interestTitle.png'
  import crashTitle from '../assets/images/crashTitle.png'
  import hotelcard from '../assets/images/hotelcard.png'
  import placecard from '../assets/images/placeCard.png'
  import crashcard from '../assets/images/crashCard.png'

  import backLetter from '../assets/images/backLetter.png'
  import btnckLetter from '../assets/images/btnckLetter.png'
  import postCancelBtn from  '../assets/images/postCancelBtn.png'
  import postBtn from  '../assets/images/postBtn.png'
  import postOutSuccessMusic from '../assets/vedio/postOut.mp3'
  import btnMusic from '../assets/vedio/btnMusic.mp3'

  import wxshare from '../store/modules/share.js'
  import { createNamespacedHelpers } from 'vuex'
  const { mapState} = createNamespacedHelpers('data/')

    export default {
        data(){
          return {
            moudelNamespace:'data/',
            logo:logo,
            btnSearchReward:btnSearchReward,
            btnBack:btnBack,
            bgImg:bgImg,
            flowerpot:flowerpot,
            mailbox:mailbox,
            storage:storage,
            showBigImgBox:false,
            bigImgShow:false,
            bigImgUrl:'',
            showRewardResult:false,
            resultTitle:resultTitle,
            noGetReward:noGetReward,
            showNoReward:false,
            closeBtn:closeBtn,
            hotelTitle:hotelTitle,
            crashTitle:crashTitle,
            interestTitle:interestTitle,
            hotelcard:hotelcard,
            placecard:placecard,
            crashcard:crashcard,
            backLetter:backLetter,
            btnckLetter:btnckLetter,
            completePost:false,
            currentDraggleImg:null,
            postBtn:postBtn,
            postOutPId:null,
            postCancelBtn:postCancelBtn,
            postOutSuccess:postOutSuccessMusic,
            rewardResultHotel:[
              {
                name:'xns',
                url:'',
                code:'123456789a',
                valueDate:'20190815',
              },
              {
                name:'四面山',
                url:'',
                code:'123456',
                valueDate:'20190815',
              },
            ],
            rewardResultCrash:[
              {
                count:0,
                money:0,
                url:''
              }
            ],
            rewardResultInterst:[
              {
                name:'四面山酒店',
                url:'',
                code:'123456789a',
                valueDate:'20190815',
              },
              {
                name:'山酒店',
                url:'',
                code:'1256',
                valueDate:'20190815',
              }
            ],
            mailboxStyle:{
              backgroundImage:'url('+mailbox+')',
              backgroundRepeat:'no-repeat',
              backgroundSize:'100% 100%',
              backgroundPosition:'0 0',
            },
            storageStyle:{
              backgroundImage:'url('+storage+')',
              backgroundRepeat:'no-repeat',
              backgroundSize:'100% 100%',
              backgroundPosition:'0 0',
              curDragImgItem:null,
            },
            cards:null,
            cardCount:0,
            btnMusic:btnMusic
          }
        },
      computed:{
        ...mapState({
          imgBaseUrl:state=>state.imgBaseUrl
        })
      },
      methods:{
        showBigPic:function(item,pid){
          let me=this
          me.bigImgShow=true
          me.bigImgUrl=item
          me.postOutPId=pid
        },
        closeBigImgBox:function(){
          let me=this
          me.bigImgShow=false

          let  myVideo=document.getElementById("btnMusic1");
          myVideo.play();

        },
        back:function(){
         this.$router.back(-1)
         },
        //查询中奖结果
        searchReward:function(){
          let me=this
          me.rewardResultCrash=[]
          me.rewardResultInterst=[]
          me.rewardResultHotel=[]
          me.showRewardResult=true
          me.$axios.get('api/prizeInfo',{hearders:{token:localStorage.getItem('userId')}})
            .then((res)=>{
             // console.log('查询中奖结果：'+JSON.stringify(res))
              if(res.data.status===0){
                if(res.data.data.length===0){
                  me.showNoReward=true
                }else{
                  for(let item of res.data.data){
                    let temp={}
                    switch(item.type){
                      case 1:
                        temp.count=item.num
                        temp.money=item.money
                        temp.url=item.url
                        me.rewardResultCrash.push(temp)
                        break
                      case 2:
                        temp.code=item.orderId
                        temp.url=me.imgBaseUrl+item.url
                        temp.name=item.name
                        me.rewardResultInterst.push(temp)
                        break
                      case 3:
                        temp.code=item.orderId
                        temp.url=me.imgBaseUrl+item.url
                        temp.name=item.name
                        me.rewardResultHotel.push(temp)
                        break
                      default :
                        temp.count=0
                        temp.money=0
                        me.rewardResultCrash.url=''
                        break
                    }
                  }
                }

              }
            })
        },
        closeRewardResult:function(){
          this.showRewardResult=false
        },
        toReward:function(){
          this.completePost=false
          let me = this
          let  myVideo=document.getElementById("btnMusic1");
               myVideo.play();
          setTimeout(function () {
           // window.location.href= '/name=reward'
             me.$router.push({name:'reward'})
          },300)
        },
        getCardInfo:function () {
          let me=this
          me.$axios.get('/api/cardInfo',{hearders:{token:localStorage.getItem('userId')}})
            .then((res)=>{
             // console.log('获取明信片详情'+res)
              if(res.data.status===0){
                let arr=[]
                me.cardCount=res.data.data.cardNum
                for(let item of res.data.data.cardInfo ){
                  let temp={}
                  temp.pid=item.pid
                  temp.url=me.imgBaseUrl+item.url
                  arr.push(temp)
                }
                if(arr.length>8){
                  me.cards=arr.slice(0,8)
                }else {
                  me.cards=arr
                }

              }else{
                me.$toast.fail(res.data.msg)
              }
            })
            .catch((err)=>{
              console.log(err)
            })

        },
        postCardOut:function(pid){
          let me=this
          me.$axios.post('/api/sendCard',{pid:pid,token:sessionStorage.getItem('userId')})
            .then((res)=>{
             //console.log('寄出明信片'+JSON.stringify(res))
              if(res.data.status===0){
                //请求寄信接口成功后操作
                //声效
                let audio = document.createElement('audio');
                audio.src=me.postOutSuccess
                document.body.appendChild(audio)
                audio.play()

                me.getCardInfo()
                me.bigImgShow=false
                me.completePost=true
                let rewardInfo={}
                rewardInfo.type=res.data.data.type
                rewardInfo.url=res.data.data.url
                rewardInfo.orderId=res.data.data.orderId
                rewardInfo.serial=res.data.data.serial
                rewardInfo.texturl=res.data.data.texturl
                if(res.data.data.type===1){
                  rewardInfo.money=res.data.data.money
                  rewardInfo.moneyUrl=res.data.data.openUrl
                  localStorage.setItem('isCrashOpened',false)
                }
               //console.log('存：'+JSON.stringify(rewardInfo))
               me.$store.commit(me.moudelNamespace +'setReward',{data:rewardInfo})
                localStorage.setItem('prizeResult',rewardInfo)
              }else{
                me.$toast(res.data.msg)
              }
            })
            .catch((err)=>{
              console.log(err)
            })

        }
      },
      created:function(){
          this.getCardInfo()
          let me=this
          wxshare.wxshare(this.$route.fullPath, sessionStorage.getItem('userId'))


      }
    }
</script>

<style lang="scss">
  .postStation{
    width: 100%;
    height: 100%;
    position: relative;
    overflow: hidden;
    .logo{
      position: absolute;;
      z-index:999;
      width: 161px;
      height: 25px;
      left:20px;
      top:20px;
    }
    .btnSearchReward{
      z-index:2;
      width: 36.5px;
      height: 43.5px;
      position:fixed;
      right:70px;
      top:20px;
    }
    .btnBack{
           z-index:2;
           width: 35px;
           height: 43.5px;
           position:fixed;
           right:24px;
           top:20px;
         }
    .bgImg{
      position: relative;
      width: 375px;
      height:664px;

      }
    .flowerpot{
     width: 84.5px;
      height: 133.5px;
      left:0;
      bottom:42px;
      z-index:2;
      position: absolute;
    }
    .mailbox{
      width: 93px;
      height: 224.5px;
      right: 0;
      bottom:0;
      z-index:2;
      position: absolute;
    }

    .textMailboxWrap{
      width: 100%;
      position: absolute;
      display: flex;
      flex-direction: column;
      align-items: center;
      z-index: 1;
      left:0;
      top:0;
      text-align: center;
      .text{
        font-size:12px;
        color:#000;
        margin-top:175px;
        margin-bottom: 10px;
        .blueNumber{
          font-size: 17px;
          color: #52c0f4;
        }
      }
      .storage{
        width: 287px;
        height: 373px;
        position: relative;
        display: flex;
        justify-content: center;
      }
      .cards{
        position: absolute;
        top:0;
        z-index:3;
        width: 197.5px;
        /*height: 320px;*/
        display: flex;
        justify-content: space-between;
        flex-wrap: wrap;
        .cardiv{
          width:90px ;
          height: 66px;
          margin-top:20px;
          margin-bottom: 5px;
          position: relative;
        }
        img{
          width:90px ;
          height: 66px;
        }
      }
    }
    .showBigImgBox{
      z-index:10;
      position:fixed;
      top:0;
      left:0;
      width: 100%;
      height: 100%;
      background-color:rgba(0,0,0,0.3) ;
      display: flex;
      justify-content: center;
      .bigImgBox{
        width: 295px;
        height:386px;
        padding-top:40px;
        margin-top:112.5px;
        position:relative;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        .closeBtn{
          width: 22.5px;
          height: 40.5px;
          position: absolute;
          right:20px;
          top:0;
        }
        .bigImg{
          width: 295px;
          height:210px;
        }
        .postBtn{
          margin-top:40px;
          width: 135.5px;
          height: 48px;
        }
        .postCancelBtn{
          margin-top:40px;
          width: 135.5px;
          height: 48px;
        }
      }
    }
    .rewardResult {
      z-index: 5;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: rgba(0, 0, 0, 0.3);
      display: flex;
      justify-content: center;
      .resultBoxWrap {
        margin-top: 69.5px;
        padding-top: 18px;
        position: relative;
        .resultTitle {
          position: absolute;
          top: 18px;
          left: 50%;
          margin-left: -104.5px;
          width: 209.5px;
          height: 36.5px;
        }
        .closeBtn {
          width: 22.5px;
          height: 40.5px;
          position: absolute;
          right: 20px;
          top: 0;
        }
        .resultBox {
          margin-top: 22.5px;
          padding: 25px;
          width: 265.5px;
          height: 451.5px;
          background-color: #8bdcff;
          border-radius: 5px;
          .rewardContainer {
            width: 265.5px;
            height: 451.5px;
            border-radius: 5px;
            background-color: #fff;
            text-align: center;
            overflow-y: scroll;
            .noGetReward {
              width: 176.5px;
              height: 154.5px;
              margin-top: 117.5px;
            }
            .rewardResutTitle {
              height: 14px;
              width: 166px;
              margin-top: 11.5px;
              margin-bottom: 11.5px;
            }
            .resultHotel {
              width: 100%;
              display: flex;
              flex-direction: column;
              align-items: center;
              .hotelCardwrap {
                width: 210.5px;
                height: 89.5px;
                position: relative;
                margin-bottom: 11.5px;
                .hotelCard {
                  width: 210.5px;
                  height: 89.5px;
                }
                .changeInfo {
                  position:absolute;
                  font-size: 10px;
                  font-weight: bold;
                  color: #1c8ebf;
                  text-align: center;
                  height: 32px;
                  width: 108px;
                  right: 0;
                  bottom: 5px;
                }
              }
              .hotelText {
                width: 210.5px;
                padding-bottom: 13px;
                text-align: left;
              }
            }
            .resultCrash {
              width: 100%;
              display: flex;
              flex-direction: column;
              align-items: center;
              .crashCardwrap {
                width: 210.5px;
                height: 89.5px;
                margin-bottom: 11.5px;
                position: relative;
                .crashCard {
                  width: 210.5px;
                  height: 89.5px;
                }
                .textInfo {
                  position:absolute;
                  font-size: 24px;
                  font-weight: bold;
                  color: #1c8ebf;
                  right: 37.5px;
                  top: 25px;
                }
              }
              .crashText {
                width: 210.5px;
                padding-bottom: 13px;
              }
            }
            .resultPlace {
              width: 100%;
              display: flex;
              flex-direction: column;
              align-items: center;
              .interestCardwrap {
                width: 210.5px;
                height: 89.5px;
                margin-bottom: 11.5px;
                position: relative;
                .placecard {
                  width: 210.5px;
                  height: 89.5px;
                }
                .changeInfo {
                  position:absolute;
                  font-size: 10px;
                  font-weight: bold;
                  color: #1c8ebf;
                  text-align: center;
                  height: 32px;
                  width: 108px;
                  right: 0;
                  bottom: 5px;
                }
              }
              .interestText {
                width: 210.5px;
                padding-bottom: 13px;
                text-align: left;
              }
            }
          }
        }
      }
    }
    .getBackLetter{
      z-index:5;
      position:fixed;
      top:0;
      left:0;
      text-align: center;
      width: 100%;
      height: 100%;
      background-color:rgba(0,0,0,0.3) ;
      .backLetterWrap{
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        .backLetter{
          width:165px;
          height:137.5px;
        }
        .btnckLetter{
          width:135.5px;
          height:50.5px;
          margin-top:32.5px;

        }
      }
    }
  }
  .scaleMinAnimation{
    animation: scaleMin 2s;
  }
  @keyframes scaleMin {
    from{
      width:90px ;
      height: 66px;
    }to{
       width:0 ;
       height:0;
       margin-top: 50px;
       margin-left: 50px;
     }
  }

</style>

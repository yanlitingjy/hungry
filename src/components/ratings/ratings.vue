<template>
    <div class="ratings" ref="ratings">
        <div class="ratings-content">
            <div class="overview">
                <div class="overview-left">
                    <h1 class="score">{{seller.score}}</h1>
                    <div class="title">综合评分</div>
                    <div class="rank">高于周边商家{{seller.rankRate}}%</div>
                </div>
                <div class="overview-right">
                    <div class="score-wrapper">
                        <span class="title">服务态度</span>
                        <star :size="36" :score="seller.serviceScore" class="serviceScore"></star>
                        <span class="score">{{seller.serviceScore}}</span>
                    </div>
                    <div class="score-wrapper">
                        <span class="title">商品得分</span>
                        <star :size="36" :score="seller.foodScore" class="serviceScore"></star>
                        <span class="score">{{seller.foodScore}}</span>
                    </div>
                    <div class="score-wrapper">
                        <span class="title">送达时间</span>
                        <span class="delTime">{{seller.deliveryTime}}分钟</span>
                    </div>
                </div>
            </div>
            <split></split>
            <ratingselect :selectType="selectType" :onlyContent="onlyContent" :ratings="ratings" ></ratingselect>
            <div class="rating-wrapper">
                <ul>
                    <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item">
                        <div class="avatar">
                            <img :src="rating.avatar" alt="" width="28" height="28">
                        </div>
                        <div class="content">
                            <h1 class="name">{{rating.username}}</h1>
                            <div class="star-wrapper">
                                <star :size="24" :score="rating.score" class="star"></star>
                                <span class="dilivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分送达</span>
                            </div>
                            <p class="text">{{rating.text}}</p>
                            <div class="recommend" v-show="rating.recommend">
                                <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
                                <span v-for="item in rating.recommend" class="rating-item">
                                    {{item}}
                                </span>
                            </div>
                             <div class="time">{{rating.rateTime | formatDate}}</div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div> 
</template>
<script>
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
const ERR_OK = 0
import star from '../star/star'
import split from '../split/split'
import ratingselect from '../ratings/ratingselect'
import {formatDate} from '../../common/js/date.js'
import BScroll from 'better-scroll'
import Bus from '../../common/js/bus.js'
export default {
    props:{
        seller:{
            type:Object
        }
    },
    created() {
        this.$axios.get('/api/ratings').then((response) => {
        response = response.data
        console.log(response);
        if (response.errno === ERR_OK) {
            this.ratings = response.datta
            this.$nextTick(()=> {
                 this.scroll = new BScroll(this.$refs.ratings,{
                        click:true
                    })
            })         
          }
        })
    },
     mounted:function(){
        this.$nextTick(()=>{
             Bus.$on('selectType',(selectType)=>{
                this.selectType=selectType;
            })
        })
       
       this.$nextTick(()=>{
           Bus.$on('ratingtype',(ratingtype)=>{
                this.onlyContent=ratingtype;
            })
       })
        
    },
     filters:{
        formatDate(time){
            //let date = new Date(time);
            return formatDate(time);
        }
    },
    data(){
        return {
            ratings:[],
            showFlag:false,
            selectType:ALL,
            onlyContent:true,
        }
    },
    methods:{
         needShow(type,text){
            if(this.onlyContent && !text){
                return false;
            }
            if(this.selectType === ALL){
                return true;
            }else{
                console.log(type);
                return type===this.selectType;
            }
        }    
    },
    components:{
        star,
        split,
        ratingselect
    }
}
</script>
<style scoped>
@import '../../common/css/font.css';
    .ratings{
        position: absolute;
        top:174px;
        bottom:0;
        left:0;
        width:100%;
        overflow: hidden;
    }
    .overview{
        display: flex;
        padding:18px 0;
    }
    .overview-left{
        flex:0 0 137px;
        width:137px;
        border-right:1px solid rgba(7,17,27,0.1);
        text-align: center;
        padding:6px 0;
    }
    .overview-left>.score{
        font-size:24px;
        color:rgb(255,153,0);
        line-height:28px;
        margin-bottom:6px;
    }
    .overview-left>.title{
        font-size:12px;
        line-height: 12px;
        color:rgb(7,17,27);
        margin-bottom:8px;
    }
    .overview-left>.rank{
        line-height: 10px;
        font-size:10px;
        color:rgb(147,153,159);
    }
    .overview-right{
        flex:1;
        padding:6px 0 6px 24px;
    }
    .score-wrapper{
        margin-bottom:8px;
        font-size: 0;
    }
    .score-wrapper>.title{
         line-height: 18px;
        display: inline-block;
        vertical-align: top;
        font-size:12px;
        color:rgb(7,17,27);
    }
    .serviceScore{
       display: inline-block;
         vertical-align: top;
        margin: 0 12px;
    }
    .score-wrapper>.score{
        display: inline-block;
        vertical-align: top;
        color:rgb(255,153,0);
        font-size:12px;
    }
    .score-wrapper>.delTime{
        font-size:12px;
        color:rgb(147,153,159);
        line-height:18px;
        margin-left:12px;
    }
     @media only screen and (max-width:320px){
        .overview-left{
            flex:0 0 120px;
            width:120px;
        }
        .overview-right{
            padding-left:6px;
        }
        .serviceScore{
            margin: 0 6px;
        }
    }
    .rating-wrapper{
        padding:0 18px;
    }
    .rating-item{
        display: flex;
        padding:18px 0;
        border-bottom:1px solid rgba(7,17,27,0.1);
    }
    .rating-item>.avatar{
        width: 28px;
        height: 28px;
        margin-right:12px;
       
    }
    .rating-item>.avatar>img{
         border-radius: 50%;
    }
    .rating-item>.content{
        position: relative;
        flex:1;
    }
    .rating-item>.content>.name{
        font-size:10px;
        color:rgb(7,17,27);
        line-height: 12px;
    }
    .star-wrapper{
        margin: 4px 0 6px;
        font-size:0;
    }
    .star-wrapper>.star{
        display: inline-block;
    }
    .star-wrapper>.dilivery{
        font-size:10px;
        font-weight: 200;
        color:rgb(147,153,159);
        line-height: 12px;
        margin-left:6px;
    }
    .rating-item>.content>.text{
        font-size:12px;
        color:rgb(7,17,27);
        line-height: 18px;
    }
    .rating-item>.content>.recommend{
        margin-top:8px;
        line-height: 16px;
    }
    .recommend>.icon-thumb_up{    
        color:rgb(0,160,220);
        font-size: 12px;
    }
    .recommend>.icon-thumb_down{
        font-size:12px;
        color:rgb(183,187,191);
        line-height:16px;
    }
    .recommend>.rating-item{
        display: inline-block;
        border:1px solid rgba(7,17,27,0.1);
        background-color: #fff;
        padding:0 6px;
        margin:0 8px;
        font-size: 9px;
        color:rgb(147,153,159);
        line-height: 16px;
        margin:0 8px 4px 0;
        border-radius: 1px;
    }
    .content>.time{
        position: absolute;
        right:0;
        top:0;
        font-size: 10px;
        font-weight: 200;
        color:rgb(147,153,159);
        line-height: 12px;

    }
</style>



<template>
    <transition name="move">
    <div class="food" v-show="showFlag" ref="food">
        <div class="food-content">
            <div class="image-header">
                <img :src="food.image" alt="">
                <div class="back" @click="hide">
                    <i class="icon-arrow_lift"></i>
                </div>             
            </div>
            <div class="content">
                <h1 class="title">{{food.name}}</h1>
                <div class="detail">
                    <span class="sell-count">月售{{food.sellCount}}</span>
                    <span class="rating">好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                    <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                    <cartcontrol :food="food"></cartcontrol>
                </div>
                <div class="buy" v-show="!food.count || food.count===0" @click.stop.prevent="addFirst">加入购物车</div>
            </div>
            <split v-show="food.info"></split>
            <div class="info" v-show="food.info">
                <h1 class="title">商品信息</h1>
                <p class="text">{{food.info}}</p>
            </div>
            <split v-show="food.info"></split>
            <div class="rating">
                <h2 class="title">商品评价</h2>
                <ratingselect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
                <div class="rating-wrapper">
                    <ul v-show="food.ratings && food.ratings.length">
                        <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item">
                            <div class="user">
                                <span class="name">{{rating.username}}</span>
                                <img :src="rating.avatar" alt="" class="avatar" width="12" height="12">
                            </div>
                            <div class="time">{{rating.rateTime | formatDate}}</div>
                            <p class="text">
                                <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
                            </p>
                        </li>
                    </ul>
                    <div class="no-rating" v-show="!food.ratings || !food.ratings.length">
                        暂无评价
                    </div>
                    
                </div>
            </div>
        </div>
    </div>
    </transition>
</template>
<script>
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
import BScroll from 'better-scroll'
import cartcontrol from '../cartcontrol/cartcontrol'
import Vue from 'Vue'
import split from '../split/split'
import ratingselect from '../ratings/ratingselect'
import Bus from '../../common/js/bus.js'
import {formatDate} from '../../common/js/date.js'
export default {
    props:{
        food:{
            type:Object
        }
    },
    data(){
        return {
            showFlag:false,
            selectType:ALL,
            onlyContent:true,
            desc:{
                all:'全部',
                positive:'推荐',
                negative:'吐槽'
            }
        }
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
    methods:{
        show(){
            this.showFlag = true;
            this.selectType = ALL,
            this.onlyContent = true;
            this.$nextTick(()=>{
                if(!this.scroll){
                    this.scroll = new BScroll(this.$refs.food,{
                        click:true
                    })
                }else{
                    this.scroll.refresh();
                }
            })
        },
        hide(){
            this.showFlag = false;
        },
        addFirst(event){
            if(event._contructed){
                return;
            }
             Vue.set(this.food,'count',1);
        },
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
    filters:{
        formatDate(time){
            //let date = new Date(time);
            return formatDate(time);
        }
    },
    components:{
        cartcontrol,
        split,
        ratingselect
    }
}
</script>
<style scoped>
@import '../../common/css/font.css';
.food{
    position: fixed;
    left:0;
    top:0;
    bottom:48px;
    z-index: 30;
    background-color: #fff;
    width: 100%;  
}
.move-enter-active,.move-leave-active{
    transition:all 0.2s linear;
    transform: translate3d(0,0,0);
}
.move-enter,.move-leave{
    transform: translate3d(100%,0,0);
}
.image-header{
    position: relative;
    width:100%;
    height:0;
    padding-top:100%;
}
.image-header>img{
    position: absolute;
    top:0;
    left:0;
    width:100%;
    height:100%;
}
.back{
    position: absolute;
    top:10px;
    left:0;
}
.icon-arrow_lift{
    display: block;
    padding:10px;
    font-size: 20px;
    color:#fff;
}
.content{
    position: relative;
    padding:18px;
}
.content>.title{
    line-height: 14px;
    margin-bottom:8px;
    font-size:14px;
    font-weight: 700;
    color:rgb(7,17,27);
}
.content>.detail{
    margin-bottom:18px;
    line-height: 10px;
    font-size:0;
}
.content>.detail>.rating,.content>.detail>.sell-count{
    font-size: 10px;
    color:rgb(147,153,159);
}
.content>.detail>.sell-count{
    margin-right:12px;
}
.price{
    line-height: 24px;
    font-weight: 700;
}
.price>.now{
    margin-right: 8px;
    font-size:14px;
    color:rgb(240, 20, 20);
}
.price>.old{
    text-decoration: line-through;
    font-size: 10px;
    color:rgb(147,153,159);
}
.cartcontrol-wrapper{
    position: absolute;
    right:12px;
    bottom:12px;
}
.buy{
    position: absolute;
    right: 18px;
    bottom:18px;
    z-index: 10;
    height:24px;
    line-height: 24px;
    padding:0 12px;
    box-sizing: border-box;
    font-size: 10px;
    border-radius: 12px;
    color:#fff;
    background-color: rgb(0,160,220);
}
.info{
    padding:18px;
}
.info>.title{
    line-height: 14px;
    margin-bottom: 6px;
    font-size: 14px;
   color:rgb(7,17,27);
}
.info>.text{
    line-height: 24px;
    padding:0 8px;
    font-size: 12px;
    color:rgb(77,85,93);
}
.rating{
    padding-top:18px;
}
.rating>.title{
    line-height: 14px;
    margin-bottom: 6px;
    font-size: 14px;
   color:rgb(7,17,27);
   padding-left:18px;
}
.rating-wrapper{
    padding:0 18px;
}
.rating-item{
    position: relative;
    padding:16px 0;
    border-bottom:1px solid rgba(7,17,27,0.1);
}
.user{
    position: absolute;
    right:0;
    top:16px;
    font-size:0;
    line-height: 12px;
}
.user>.name{
    display: inline-block;
    vertical-align: top;
    font-size:10px;
    color:rgb(147,153,159);
    margin-right:6px;
}
.user>.avatar{
    border-radius: 50%;
}
.time{
    font-size:10px;
    line-height: 12px;
    color:rgb(147,153,159);
    margin-bottom:6px;
}
.text{
    line-height: 16px;
    font-size:12px;
    color:rgb(7,17,27);
}
.icon-thumb_up,.icon-thumb_down{
    margin-right:4px;
    line-height: 16px;
    font-size: 12px;
}
.icon-thumb_up{
    color:rgb(0,160,220);
}
.icon-thumb_down{
    color:rgb(147,153,159);
}
.no-rating{
    padding:16px 0;
    font-size:12px;
     color:rgb(147,153,159);
}
</style>
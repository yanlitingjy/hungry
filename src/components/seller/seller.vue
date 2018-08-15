<template>
<div class="seller" ref="seller">
    <div class="seller-content">
        <div class="overview">
            <h1 class="title">{{seller.name}}</h1>
            <div class="desc">
                <star :size="36" :score="seller.score" class="star"></star>
                <span class="text">({{seller.ratingCount}})</span>
                <span class="text">月售{{seller.sellCount}}单</span>
            </div>
            <ul class="remark">
                <li class="block">
                    <h2>起送价</h2>
                    <div class="content">
                        <span class="stress">{{seller.minPrice}}</span>元
                    </div>
                </li>
                 <li class="block">
                    <h2>商家配送</h2>
                    <div class="content">
                        <span class="stress">{{seller.deliveryPrice}}</span>元
                    </div>
                </li>
                <li class="block">
                    <h2>平均配送时间</h2>
                    <div class="content">
                        <span class="stress">{{seller.deliveryTime}}</span>元
                    </div>
                </li>
            </ul>
            <div class="favorite" @click="toggleFavorite">
                <span class="icon-favorite" :class="{active:favorite}"></span>
                <span class="text">{{favoriteText}}</span>
            </div>
        </div>
        <split></split>
        <div class="bulletin">
            <h1 class="title">公告与活动</h1>
            <div class="content">{{seller.bulletin}}</div>
             <ul v-if="seller.supports" class="supports">
                <li class="support-item" v-for="item in seller.supports">
                    <span class="icon" :class="classMap[item.type]"></span>
                    <span class="text">{{item.description}}</span>
                </li>
            </ul>
        </div>
        <split></split>
        <div class="pics">
            <h1 class="title">商家实景</h1>
            <div class="pic-wrapper" ref="picWrapper">
                <ul class="pic-list" ref="picList">
                    <li class="pic-item" v-for="pic in seller.pics">
                        <img :src="pic" alt="" width="120" height="90">
                    </li>
                </ul>
            </div>
        </div>
        <split></split>
        <div class="infos">
            <h1 class="title">商家信息</h1>
            <ul class="infoList">
                <li class="infoItem" v-for="info in seller.infos">{{info}}</li>
            </ul>
        </div>
    </div>
</div> 
</template>
<script>
import BScroll from 'better-scroll'
import star from '../star/star'
import split from '../split/split'
import shopcart from '../shopcart/shopcart'
import {saveToLocal} from '../../common/js/store.js'
import {loadFromLocal} from '../../common/js/store.js'
export default {
    props:{
        seller:{
            type:Object
        }
    },
    data() {
        return {
            favorite:(()=>{
                return loadFromLocal(this.seller.id,'favorite',false)
            })()
        }
    },
    computed:{
        favoriteText(){
            return this.favorite?'已收藏':'收藏'
        }
    },
    created() {
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    },
    mounted() {
        this.$nextTick(()=>{
            if(!this.scroll){
                this.scroll = new BScroll(this.$refs.seller,{
                    click:true
                })
            }else{
                this.scroll.refresh();
            }
        })
    },
    methods:{
        toggleFavorite(event){
            if(!event._constructed){
                return;
            }
            this.favorite = !this.favorite;
            saveToLocal(this.seller.id,'favorite',this.favorite);
        }
    },
    components:{
        star,
        split,
        shopcart
    }
}
</script>
<style scoped>
@import '../../common/css/font.css';
.seller{
    position: absolute;
    top:174px;
    bottom:0;
    left:0;
    width:100%;
    overflow: hidden;
}
.overview{
    padding:18px;
    position: relative;
}
.overview>.title{
    font-size:14px;
    color:rgb(7,17,27);
    line-height: 14px;
    margin-bottom:8px;
}
.overview>.desc{
    padding-bottom:18px;
    border-bottom:1px solid rgba(7,17,27,0.1);
    font-size:0;
}
.overview>.desc>.star{
    display:inline-block;
    vertical-align:top;
    margin-right:8px;
}
.overview>.desc>.text{
    display:inline-block;
    vertical-align: top;
    font-size:10px;
    color:rgb(77,85,93);    
     margin-right:12px;
     line-height:18px;
}
.remark{
    display:flex;
    margin-top:18px;
}
.remark>.block{
    flex:1;
    text-align: center;
    border-right:1px solid rgba(7,17,27,0.1);
}
.remark>.block:last-child{
    border-right:0;
}
.remark>.block>h2{
    font-size:10px;
    color:rgb(147,153,159);
    line-height:10px;
    margin-bottom:4px;
}
.remark>.block>.content{
    color:rgb(7,17,27);
    font-size:10px;
    line-height: 24px;
    font-weight: 200;
}
.remark>.block>.content>.stress{
    font-size:24px;
}
.favorite{
    width: 50px;
    position: absolute;
    right:11px;
    top:18px;
    text-align: center;
}
.favorite>.icon-favorite{
    display:block;
    color:#d4d6d9;
    line-height: 24px;
    font-size: 24px;
    margin-bottom:4px;
}
.favorite>.icon-favorite.active{
    color:rgb(240,20,20)
}
.favorite>.text{
    font-size:10px;
    color:rgb(77,85,93);
    line-height: 10px;
}
.bulletin{
    padding-top:18px;
    margin:0 18px;
    font-size:0;
}
.bulletin>.title{
    margin-bottom:8px;
    font-size:14px;
    color:rgb(7,17,27);
}
.bulletin>.content{
    padding:0 12px 18px;
    font-size:12px;
    color:rgb(240,20,20);
    line-height:24px;
     border-bottom:1px solid rgba(7,17,27,0.1);
}
.support-item{
    padding:16px 12px;
    border-bottom:1px solid rgba(7,17,27,0.1);
}
.support-item:last-child{
    border:none;
}
.support-item>.icon{
    width: 16px;
    height: 16px;
    display: inline-block;
    margin-right:6px;
    background-size: 16px 16px;
    background-repeat: no-repeat;
}
.decrease{
    background:url('decrease_4@2x.png');
}
.discount{
    background:url('discount_4@2x.png');
}
.guarantee{
    background:url('guarantee_4@2x.png');
}
.invoice{
    background:url('invoice_4@2x.png');
}
.special{
    background:url('special_4@2x.png');
}
.support-item>.text{
    display: inline-block;
    vertical-align: top;
    font-size:12px;
    color:rgb(7,17,27);
    line-height: 16px;
}
.pics{
   padding:18px;
}
.pics>.title{
    margin-bottom:12px;
    font-size:14px;
    color:rgb(7,17,27);
    line-height: 14px;
}
.pic-wrapper{
    width: 100%;
    overflow: hidden;
    white-space: nowrap;
}
.pic-wrapper>.pic-list{
    font-size:0;
}
.pic-wrapper>.pic-list>.pic-item{
    display: inline-block;
    margin-right: 6px;
    width: 120px;
    height: 90px;
}
.pic-wrapper>.pic-list>.pic-item:last-child{
    margin-right:0;
}
.infos{
   padding:18px; 
}
.infos>.title{
    margin-bottom:12px;
    font-size:14px;
    color:rgb(7,17,27);
    line-height: 14px;
}
.infos .infoItem{
    padding: 16px 12px;
    border-top:1px solid rgba(7,17,27,0.1);
    font-size:12px;
    color:rgb(7,17,27);
    line-height: 16px;
}
</style>

<template>
<div>
    <div class="shopcart">
         <div class="content" @click = "toggleList">
             <div class="content-left">
                 <div class="logo-wrapper">
                     <div class="logo" :class="{'highlight':totalCount>0}">
                         <span class="icon-shopping_cart" :class="{'color':totalCount>0}"></span>
                     </div>
                     <div class="num" v-show="totalCount>0">{{totalCount}}</div>
                 </div>
                  <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
                  <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
             </div>
             <div class="content-right" @click.stop.prevent="pay" >
                 <div class="pay" :class="payClass">{{payDesc}}</div>               
            </div>
         </div>
         <div class="ball-container">
            <transition-group name="drop" tag="div"  @before-enter="beforeEnter" @enter="dropEnter" @after-enter="afterEnter" >
                <div v-for="(ball,index) in balls" v-show="ball.show" class="ball" :key="index">
                     <div class="inner inner-hook"></div>
                </div>
            </transition-group>
        </div>
        <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
            <div class="list-header">
                <div class="title">购物车</div>
                <div class="empty" @click="empty">清空</div>
            </div>
            <div class="list-content" ref="listContent">
                <ul>
                    <li class="food" v-for="food in selectFoods">
                        <span class="name">{{food.name}}</span>
                        <div class="price">
                            <span>￥{{food.price*food.count}}</span>
                        </div>
                        <div class="cartcontrol-wrapper">
                           <cartcontrol :food="food" ></cartcontrol>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
        </transition>
    </div>
    <transition name="fade">
        <div class="list-mask" v-show="listShow" @click="hidelist"></div>
    </transition>
</div>
</template>

<script>
import BScroll from 'better-scroll'
import cartcontrol from '../cartcontrol/cartcontrol'
export default {
    props: {
        selectFoods: {
            type: Array,
            default() {
                return []
            }
        },
        deliveryPrice: {
            type: Number,
            default: 0
        },
        minPrice: {
            type: Number,
            default: 0
        }
    },
    data() {
        return {
            balls:[
                {
                    show:false,
                    el:null
                },
                {
                    show:false,
                    el:null
                },
                {
                    show:false,
                    el:null
                },
                {
                    show:false,
                    el:null
                },
                {
                    show:false,
                    el:null
                }
            ],
            dropBall :[],
            fold:true
        }
    },
    computed: {
        totalPrice() {
            let total = 0;
            this.selectFoods.forEach((food)=> {
                total += food.price * food.count
            });
            return total
        },
        totalCount() {
            let count = 0;
            this.selectFoods.forEach((food)=>{
                count += food.count
            });
            return count;
        },
        payDesc() {
            if(this.totalPrice === 0){
                return `￥${this.minPrice}元起送`;
            }else if(this.totalPrice < this.minPrice) {
                let diff = this.minPrice - this.totalPrice;
                return `还差￥${diff}元起送`;
            }else{
                return '去结算';
            }
        },
        payClass() {
            if(this.totalPrice < this.minPrice){
                return 'not-enough'
            }else{
                return 'enough'
            }
        },
        listShow() {
            if(!this.totalCount){
                this.fold = true;
                return false;
            }
            let show = !this.fold;
            if(show){
                this.$nextTick(()=>{
                    if(!this.scroll){
                        this.scroll = new BScroll(this.$refs.listContent,{
                            click:true
                        })
                    }else{
                        this.scroll.refresh();
                    }
                })
            }
            return show;
        }
    },
    methods: {
        pay(){
            if(this.totalPrice<this.minPrice){
                return;
            }
            window.alert(`支付${this.totalPrice}元`);
        },
        empty(){
            this.selectFoods.forEach((food)=>{
                food.count = 0;
            })
        },
        toggleList(){
            if(!this.totalCount){
                return ;
            }
            this.fold = !this.fold;
        },
        hidelist(){
            this.fold = true;
        },
        drop(el){
           for(let i=0;i<this.balls.length;i++){
               let ball = this.balls[i];
               if(!ball.show){
                   ball.show = true;
                   ball.el = el;
                   this.dropBall.push(ball);
                   return ;
               }
           }
        },
         beforeEnter(el,done) {
            let count = this.balls.length; 
            while(count--){
                let ball = this.balls[count];
                if(ball.show){
                    let rect = ball.el.getBoundingClientRect();//获得元素相对于视口的位置
                    let x = rect.left-32;
                    let y = -(window.innerHeight - rect.top - 22);
                    //console.log(y);
                    el.style.display = '';
                    el.style.webkitTransform = `translate3d(0,${y}px,0)`;
                    el.style.transform = `translate3d(0,${y}px,0)`;
                    let inner = el.getElementsByClassName('inner-hook')[0];
                    inner.style.webkitTransform =  `translate3d(${x}px,0,0)`;
                    inner.style.transform =  `translate3d(${x}px,0,0)`;
                }
            }
        },
        dropEnter(el,done) {
            let rf = el.offsetHeight;
            this.$nextTick(()=>{
                el.style.webkitTransform = "translate3d(0,0,0)";
                el.style.transform = "translate3d(0,0,0)";
                let inner = el.getElementsByClassName('inner-hook')[0];
                inner.style.webkitTransform =  "translate3d(0,0,0)";
                inner.style.transform = " translate3d(0,0,0)";
                //el.addEventListener('transitionend', done);
            })
        },
        afterEnter(el) {
            let ball = this.dropBall.shift();
            if(ball){
                //el.style.display = "none";
                ball.show = false;
                ball.el = null;
            }
        }
    },
    components: {
        cartcontrol
    }
           
}
</script>

<style scoped>
@import '../../common/css/font.css';
.shopcart{
    position: fixed;
    left:0;
    bottom:0;
    z-index: 50;
    width:100%;
    height:48px;
    /* background-color: #000; */
}
.shopcart .content{
    display:flex;
    background-color: #141d27;
    font-size:0;
}
.content>.content-left{
    flex:1;
}
.content>.content-left>.logo-wrapper{
    display: inline-block;
    position:relative;
    top:-10px;
    margin:0 12px;
    padding:6px;
    width: 56px;
    height: 56px;
    box-sizing: border-box;
    vertical-align: top;
    border-radius: 50%;
    background-color: #141d27;
}
.content>.content-left>.logo-wrapper>.logo{
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background-color:#2b343c;
    text-align: center;
}
.content>.content-left>.logo-wrapper>.logo.highlight{
    background-color: rgb(0,160,220);
}
.content>.content-left>.logo-wrapper>.num{
    position: absolute;
    top:0;
    right:0;
    width: 24px;
    height: 16px;
    line-height: 16px;
    text-align: center;
    border-radius: 16px;
    font-size: 9px;
    font-weight: 700;
    color:#fff;
    background-color: rgb(240,20,20);
    box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4);
}
.icon-shopping_cart{
    font-size:24px;
    color:#80858a;
    line-height: 44px;
}
.icon-shopping_cart.color{
    color:#fff;
}
.content-left>.price{
    display: inline-block;
    vertical-align: top;
    line-height:24px;
    margin-top:12px;
    padding-right:12px;
    box-sizing:border-box;
    border-right:1px solid rgba(255,255,255,0.1);
    font-size:16px;
    font-weight:700;
    color:rgba(255,255,255,0.4);
}
.content-left>.price.highlight{
    color:#fff;
}
.content-left>.desc{
    display: inline-block;
    color:rgba(255,255,255,0.4);
    font-size:12px;
    font-weight:700;
    line-height: 24px;
    padding-left:12px;
    margin-top:12px;
    vertical-align: top;
}
.content>.content-right{
    width:105px;
    flex:0 0 105px;
}
.content>.content-right>.pay{
    font-size:12px;
    color:rgba(255,255,255,0.4);
    font-weight: 700;
    line-height: 24px;
    background-color: #2b343c;
    text-align:center;
    padding:12px 0;
}
.content>.content-right>.pay.not-enough{
    background-color: #2b343c;
}
.content>.content-right>.pay.enough{
    background-color: #00b43c;
    color:#fff;
}
.ball-container .ball{
    position: fixed;
    left:32px;
    bottom:22px;
    z-index: 200;
    transition:all 1s cubic-bezier(0.49, -0.29, 0.75, 0.41);
}
.ball-container .ball .inner{
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background-color: rgb(0,160,220);
    transition:all 1s linear;
}
.shopcart-list{
    position: absolute;
    left:0;
    bottom:48px;
    z-index:-1;
    width:100%;
    transition:all 0.5s ease-in;
}
.fold-transition{
    transform: translate3d(0,-100%,0);
}
.fold-enter-active,.fold-leave-active{
    transform: translate3d(0,0,0); 
}
.list-header{
    height: 40px;
    line-height: 40px;
    padding:0 18px;
    background:#f3f5f7;
    border-bottom:1px solid rgba(7,17,27,0.1);
}
.list-header>.title{
    float:left;
    font-size:14px;
    color:rgb(7,17,27);
}
.list-header>.empty{
    float:right;
    font-size:12px;
    color:rgb(0,160,220);
}
.list-content{
    padding:0 18px;
    max-height:217px;
    background:#fff;
    overflow: hidden;
}
.list-content .food{
    position: relative;
    padding:12px 0;
    box-sizing: border-box;
    border-bottom:1px solid rgba(7,17,27,0.1);
}
.list-content .food>.name{
    line-height: 24px;
    font-size:14px;
    color:rgb(7,17,27);
}
.list-content .food>.price{
    position: absolute;
    right:90px;
    bottom:12px;
    line-height: 24px;
    font-weight: 700;
    color:rgb(240,20,20);
}
.list-content .food>.cartcontrol-wrapper{
    position: absolute;
    right:0;
    bottom:6px;
}
.list-mask{
    position: fixed;
    top:0;
    left:0;
    width:100%;
    height: 100%;
    z-index:40;
    backdrop-filter: blur(10px);
    transition: all 0.5s;
    background-color: rgba(7,17,27,0.6);
}
.fade-transition{
    opacity: 1;
    background:rgba(7,17,27,0.6);
}
.fade-enter,.fade-leave{
    opacity: 0;
    background:rgba(7,17,27,0);
}
</style>


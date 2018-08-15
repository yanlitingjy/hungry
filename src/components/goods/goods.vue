<template>
    <div class="goods">
        <div class="menu-wrapper" ref="menuWrapper">
            <ul>
                <li v-for="(item,index) in goods" class="menu-item" :class="{'current': currentIndex == index}" @click="selectMenu(index)">
                    <span class="text"><span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}</span>                   
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" ref="foodsWrapper">
            <ul>
                <li class="food-list border-1px food-list-hook" v-for="item in goods">
                    <h1 class="title">{{item.name}}</h1>
                    <ul>
                         <li v-for="food in item.foods" class="food-item" @click="selectFood(food,$event)">
                            <div class="icon">
                                <img :src="food.icon" alt="">
                            </div>
                            <div class="content">
                                <h2 class="name">{{food.name}}</h2>
                                <p class="desc">{{food.description}}</p>
                                <div class="extra">
                                    <span class="count">月售{{food.sellCount}}</span><span>好评率{{food.rating}}%</span>
                                </div>
                                <div class="price">
                                    <span class="now">¥{{food.price}}</span>
                                    <span v-show="food.oldPrice" class="old">¥{{food.oldPrice}}</span>
                                </div>
                                <div class="cartcontrol-wrapper">
                                    <cartcontrol :food="food" ></cartcontrol>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
        <shopcart ref="shopcart" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice" :selectFoods="selectFoods"></shopcart>
        <food :food="selectedFood" ref="food"></food>
    </div>
</template>
<script>
import BScroll from 'better-scroll'
import shopcart from '../shopcart/shopcart'
import cartcontrol from '../cartcontrol/cartcontrol'
import food from '../food/food'
const ERR_OK = 0
export default {
    props: {
        seller: {
            type: Object
        }
    },
    created() {
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
        this.$axios.get('/api/goods').then((response) => {
        response = response.data
        if (response.errno === ERR_OK) {
            this.goods = response.data
            this.$nextTick(()=> {
                 this._initScroll()
                 this.getGoodsHeight()
            })         
          }
        })
    },
    data() {
        return {
            goods: [],
            listHeight: [],
            scrollY: 0,
            selectedFood:{}
        }
    },
    computed: {
        currentIndex() {
            for(let i=0; i<this.listHeight.length; i++){
                let height1 = this.listHeight[i];
                let height2 = this.listHeight[i+1];
                if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
                    return i
                }
            }
        },
        selectFoods() {
            let foods = [];
            this.goods.forEach((good)=> {
                good.foods.forEach((food)=> {
                    if(food.count) {
                        foods.push(food);
                    }
                })
            })
            return foods;
        }
    },
    methods: {
        selectFood(food,$event){
            if(!event._constructed){
               return;
           }
            this.selectedFood = food;
            this.$refs.food.show();
        },
        // cartAdd(el) {
        //     this.$nextTick(() =>{
        //         this.$refs.shopcart.drop(el);
        //     })
        // },
        _initScroll() {
            this.menuScroll = new BScroll(this.$refs.menuWrapper, {
                click: true
            });
            this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
                probeType: 3,
                 click: true
            })
            this.foodsScroll.on('scroll',(pos)=> {
                console.log(pos);
                console.log(Math.round(pos.y));
                this.scrollY = Math.abs(Math.round(pos.y))
            })
        },
       getGoodsHeight() {
           let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
           let height = 0
           this.listHeight.push(height)
           for(let i=0; i< foodList.length; i++) {
               let item = foodList[i]
               height += item.clientHeight
               this.listHeight.push(height)
           }
       },
       selectMenu(index) {
           if(!event._constructed){
               return;
           }
           let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
           let el = foodList[index];
           this.foodsScroll.scrollToElement(el,300)
       },
       _drop(target) {
           this.$nextTick(()=>{
               this.$refs.shopcart.drop(target);
           })       
       }
    },
    components: {
        shopcart,
        cartcontrol,
        food
    },
    events:{
        'cart-add'(target){
            this._drop(target);
        }
    }
}
</script>
<style scoped>
@import '../../common/css/font.css';
.goods{
    display: flex;
    position:absolute;
    width: 100%;
    top:174px;
    bottom:46px;   
    overflow: hidden;
}
.goods>.menu-wrapper{
    flex:0 0 80px;
    width: 80px;
    background:#f3f5f7;
}
.current{
    position: relative;
    z-index: 10;
    margin-top:-1;
    background-color: #fff;
    font-weight: 700;
}
.menu-item{
    display:table;
    height: 54px;
    width: 56px;
    line-height: 14px;
    padding:0 12px;
}
.menu-item .icon{
    width: 12px;
    height: 12px;
    display: inline-block;
    margin-right: 2px;
    background-size: 12px 12px;
    background-repeat: no-repeat;
}
.menu-item .text{
    font-size:12px;
    display: table-cell;
    width: 56px;
    vertical-align: middle;
    border-bottom:1px solid rgba(7,17,27,0.1);
}
.decrease{
    background:url('decrease_3@2x.png');
}
.discount{
    background:url('discount_3@2x.png');
}
.guarantee{
    background:url('guarantee_3@2x.png');
}
.invoice{
    background:url('invoice_3@2x.png');
}
.special{
    background:url('special_3@2x.png');
}
.goods>.foods-wrapper{
    flex:1;
}
.foods-wrapper{
    flex:1;
}
.foods-wrapper .title{
    padding-left:14px;
    height:26px;
    line-height:26px;
    border-left:2px solid #d9dde1;
    font-size:12px;
    color:rgb(147,153,159);
    background-color: #f3f5f7;
}
.food-item{
    display:flex;
    margin:18px;
    padding-bottom:18px;
    position:relative;
}
.border-1px{
  position: relative;
}
.border-1px:after{
  position: absolute;
  left:0;
  bottom:0;
  width: 100%;
  border-top: 1px soild rgba(7,17,27,0.1);
  content:'';
  -webkit-transform: scaleY(0.5);
  transform: scaleY(0.5);
}
.food-item:last-child{
    margin-bottom:0;
}
.food-item .icon{
    flex:0 0 57px;
    margin-right:10px;
}
.food-item .content{
    flex:1;
}
.food-item .content .name{
    margin:2px 0 8px 0;
    height:12px;
    line-height: 12px;
    font-size:14px;
    color:rgb(7,17,27)
}
.food-item .icon>img{
    width: 57px;
    height: 57px;
}
.food-item .desc{
    margin-bottom:8px;
    font-size:10px;
    line-height: 14px;
    color:rgb(147,153,159);
}
.food-item .extra{
    font-size:10px;
    line-height: 10px;
    color:rgb(147,153,159);
}
.food-item .extra .count{
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
    right:0;
    bottom:12px;
}
</style>



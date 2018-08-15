<template>
    <div class="cartcontrol">
        <transition name="move">
            <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart">
                <span class="icon-remove_circle_outline"></span>
            </div>
        </transition>
        <div class="cartcontrol"  v-show="food.count>0">{{food.count}}</div>
        <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
    </div>
</template>

<script>
import Vue from 'vue';
export default {
    props: {
        food: {
            type: Object
        }
    },
    // created() {
    //     console.log(this.food);
    // },
    methods: {
        addCart() {
            if(!this.food.count){
                Vue.set(this.food,'count',1)
                this.food.count = 1
            }else{
                this.food.count++
            }
            this.$emit('cart-add',event.target);
       },
       decreaseCart(){
           if(this.food.count>0){
                this.food.count--
            }
       }
    }
}
</script>


<style scoped>
@import '../../common/css/font.css';
.cartcontrol{
    font-size:0;
}
.cartcontrol>.cart-decrease{
    display: inline-block;
    padding: 6px;
     transition:all 0.3s linear;
}
.cart-decrease>.icon-remove_circle_outline{
     display: inline-block;
     line-height: 24px;
     font-size:24px;
     color:rgb(0,160,220);
     transition:all 0.3s linear;
     transform: rotate(0);
}
.move-transition{
    opacity: 1;
    transform: translate3d(0,0,0);
}
.move-enter-active,.move-leave-active{
    opacity: 0;
    transform: translate3d(24px,0,0); 
}
.move-enter-active,.move-leave-active>.icon-remove_circle_outline{
     transform: rotate(180deg);
}
.cartcontrol>.cartcontrol{
     display: inline-block;
     vertical-align: top;
     width: 12px;
     padding-top:6px;
     line-height: 24px;
     text-align: center;
     font-size: 10px;
     color:rgb(147,153,149);
}
.cartcontrol>.cart-add{
     display: inline-block;
     line-height: 24px;
     font-size:24px;
      padding: 6px;
      color:rgb(0,160,220);
}
</style>

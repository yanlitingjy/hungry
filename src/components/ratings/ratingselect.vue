<template>
<div class="ratingSelect">
    <div class="rating-type">
        <span @click="select(2,$event)" class="block positive" :class="{'active':typeSelect===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
        <span @click="select(0,$event)" class="block positive" :class="{'active':typeSelect===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
        <span @click="select(1,$event)" class="block negative" :class="{'active':typeSelect===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div @click="tottleContent" class="switch" :class="{'on':ratingtype}">
        <span class="icon-check_circle"></span>
        <span class="text">只看内容的评价</span>
    </div>
</div>
</template>
<script>
import Bus from '../../common/js/bus.js'
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
export default {
    props:{
        ratings:{
            type:Array,
            default(){
                return [];
            }
        },
        selectType:{
            type:Number,
            default:ALL
        },
        onlyContent:{
            type:Boolean,
            default:false
        },
        desc:{
            type:Object,
            default(){
                return {
                    all:'全部',
                    positive:'满意',
                    negative:'不满意',
                };
            }
        }
    },
    data(){
        return{
            typeSelect:this.selectType,
            ratingtype:this.onlyContent
        }
    },
    methods:{
        select(type,event){
            if(!event._constructed){
                return;
            }
            this.typeSelect = type;
            Bus.$emit('selectType',this.typeSelect);
        },
        tottleContent(event){
             if(!event._constructed){
                return;
            }
            this.ratingtype = !this.ratingtype;
            Bus.$emit('ratingtype',this.ratingtype);
        }
    },
    computed:{
        positives(){
            return this.ratings.filter((rating)=>{
                return rating.rateType === POSITIVE
            })
        },
        negatives(){
             return this.ratings.filter((rating)=>{
                return rating.rateType === NEGATIVE
            })
        }
    }
}
</script>

<style scoped>
@import '../../common/css/font.css';
.rating-type{
    padding:18px 0;
    margin:0 18px;
    border-bottom:1px solid rgba(7,17,27,0.1);
    font-size:0;
}
.block{
    display: inline-block;
    padding:8px 12px;
    margin-right:8px;
    border-radius: 1px;
    color:rgb(77,85,93);
    font-size:12px;
}
.block>.count{
    font-size:8px;
    margin-left:2px;
    line-height: 16px;
}
.positive{
    background-color: rgba(0,160,220,0.2);
}
.positive.active{
    background-color: rgba(0,160,220);
    color:#fff;
}
.negative{
    background-color: rgba(77,85,93,0.2);
}
.negative.active{
    background-color: rgba(77,85,93);
    color:#fff;
}
.switch{
    padding:12px 18px;
    line-height: 24px;
   border-bottom:1px solid rgba(7,17,27,0.1);
   color:rgb(147,153,159);
   font-size:0;
}
.switch.on .icon-check_circle{
    color:#00c850;
}
.icon-check_circle{
    font-size:24px;
    margin-right:4px;
    display:inline-block;
    vertical-align: top;
}
.switch .text{
    font-size: 12px;
}
</style>
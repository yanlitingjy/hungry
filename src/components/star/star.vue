<template>
    <div class="star" :class="starType">
        <span class="star-item" v-for="itemClass in itemClasses" :class="itemClass"></span>
    </div>
</template>

<script>
const LENGTH = 5
const CLS_ON = 'on'
const CLS_HALE = 'half'
const CLS_OFF = 'off'
export default {
    props: {
        size: {
            type: Number
        },
        score: {
            type: Number
        }
    },
    computed: {
        starType() {
            return 'star-' + this.size
        },
        itemClasses() {
            let result = []
            let score  = Math.floor(this.score * 2) / 2
            let hasDecimal = score % 1 !== 0
            let integer = Math.floor(score)
            for (let i = 0; i < integer; i++) {
                result.push(CLS_ON)
            }
            if (hasDecimal) {
                result.push(CLS_HALE)
            }
            while (result.length < LENGTH) {
                result.push(CLS_OFF)
            }
            return result
        }
    }
}
</script>

<style scoped>
    .star{
        font-size:0;
    }
    .star-item{
        display: inline-block;
        background-repeat: no-repeat;
    }
    .star-24>.star-item{
        width: 10px;
        height: 10px;
        margin-right: 3px;
    }
    .star-24>.star-item:last-child{
        margin-right:0;
    }
    .star-24>.star-item.on{
        background:url('star24_on@2x.png');
        background-size: 10px 10px;
    }
    .star-24>.star-item.half{
        background:url('star24_half@2x.png');
         background-size: 10px 10px;
    }
    .star-24>.star-item.off{
        background:url('star24_off@2x.png');
         background-size: 10px 10px;
    }
    .star-36>.star-item{
        width: 15px;
        height: 15px;
        margin-right: 8px;
    }
    .star-36>.star-item:last-child{
        margin-right:0;
    }
    .star-36>.star-item.on{
        background:url('star36_on@2x.png');
        background-size: 15px 15px;
    }
    .star-36>.star-item.half{
        background:url('star36_half@2x.png');
        background-size: 15px 15px;
    }
    .star-36>.star-item.off{
        background:url('star36_off@2x.png');
        background-size: 15px 15px;
    }
    .star-48>.star-item{
        width: 20px;
        height: 20px;
        margin-right: 22px;
    }
    .star-48>.star-item:last-child{
        margin-right:0;
    }
    .star-48>.star-item.on{
        background:url('star48_on@2x.png');
        background-size: 20px 20px;
    }
    .star-48>.star-item.half{
        background:url('star48_half@2x.png');
        background-size: 20px 20px;
    }
    .star-48>.star-item.off{
        background:url('star48_off@2x.png');
        background-size: 20px 20px;
    }
</style>


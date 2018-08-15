<template>
  <div>
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
        <div class="tab-item"><router-link to="/goods">商品</router-link></div>
        <div class="tab-item"><router-link to="/ratings">评价</router-link></div>
        <div class="tab-item"><router-link to="/seller">商家</router-link></div>
    </div>
    <!-- <div class="content">
      I am content
    </div> -->
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>
<script>
import header from './components/header/header'
import {urlParse} from './common/js/util';
const ERR_OK = 0
export default {
  created() {
    this.$axios.get('/api/seller?id='+this.seller.id).then((response) => {
      response = response.data
      if (response.errno === ERR_OK) {
       // this.seller = response.data
        this.seller = Object.assign({},this.seller,response.data);
        //console.log(this.seller.id);
      }
    })
  },
  data() {
    return {
      seller: {
        id:(()=>{
          let queryParam = urlParse();
          console.log(queryParam);
          return queryParam.id;
        })()
      }
    }
  },
  components: {
    'v-header': header
  }
}
</script>

<style scoped>
.tab{
  display:flex;
  width: 100%;
  height: 40px;
  line-height:40px;
  border-bottom:1px solid rgba(7,17,27,0.1);
}
.border-1px{
  position: relative;
}
.border-1px:after{
  position: absolute;
  left:0;
  bottom:0;
  width: 100%;
  border-top:1px soild rgba(7,17,27,0.1);
  content:'';
  -webkit-transform: scaleY(0.5);
  transform: scaleY(0.5);
}
.tab-item{
  flex:1;
  text-align: center;
}
.tab-item>a{
  display:block;
  font-size:14px;
  color:rgb(77,85,93);
}
.tab-item>a.active{
  color:rgb(240,20,20)
}
</style>

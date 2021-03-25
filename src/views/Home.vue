<template>
  <div id='home' class="home">
    <van-search v-model="searchData" placeholder="商品搜索 共239亿款好物" input-align='center' />
    <van-swipe :autoplay="3000" width="375" height='200'>
      <van-swipe-item v-for="(image, index) in images" :key="index">
        <img class="swiperimg" v-lazy="image.image_url" />
      </van-swipe-item>
    </van-swipe>

    <van-grid :column-num='5'>
      <van-grid-item v-for="(channe,index) in channel" :key='index' :icon="channe.icon_url" :text="channe.name" />
      
    </van-grid>
    <div class="brandlist">
      <van-panel title="品牌制造商直供">
      <div>
        <van-grid  :column-num="2">
        <van-grid-item v-for="(brand,index) in brandList" :key='index'>
          <van-image fit='cover' width="100%" height='100%' lazy-load :src='brand.new_pic_url' />
          <h4 class="name">{{brand.name}}</h4>
          <p class="price">{{brand.floor_price}}元起</p>
        </van-grid-item>
      </van-grid>
      </div>
    </van-panel>
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    </div>
    <div class="newgoodslist">
      <van-panel title="周一周四·新品首发">
      <div>
        <van-grid  :column-num="2">
        <van-grid-item v-for="(newGoods,index) in newGoodsList" :key='index'>
          <van-image fit='cover' width="100%" height='100%' lazy-load :src='newGoods.list_pic_url' />
          <h4 class="name" >{{newGoods.name}}</h4>
          <p class="price">{{newGoods.retail_price}}元起</p>
        </van-grid-item>
      </van-grid>
      </div>
    </van-panel>
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    </div>

    <div class="hotlist">
      <van-panel title="人气推荐">
      <van-card
        v-for="(hotGood,index) in hotGoodsList"
        :key="index"
        :price="hotGood.retail_price"
        :desc="hotGood.goods_brief"
        :title="hotGood.name"
        :thumb="hotGood.list_pic_url"
      />
    </van-panel>
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    </div>
    <div style="height:50px;"></div>
    <tab-btn></tab-btn>
    </div>
    
</template>


<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
import axios from 'axios'
import api from '@/assets/config/api'
import Vue from 'vue';
import { Lazyload } from 'vant';
import tabBtn from '@/components/tabBtn'
Vue.use(Lazyload);


export default {
  name: 'Home',
  data:function(){
    return{
      searchData:'',
      // images:[],
      data:{},
      tabActive:0
    }
  },
  computed:{
    images:function(){
      if(typeof this.data.banner=='object'){
        return this.data.banner
      }else{
        return []
      }
      
    },
    channel:function(){
      if(typeof this.data.channel=='object'){
        return this.data.channel
      }else{
        return []
      }
    },
    brandList:function(){
      if(typeof this.data.brandList=='object'){
        return this.data.brandList
      }else{
        return []
      }
    },
    newGoodsList:function(){
      if(typeof this.data.newGoodsList=='object'){
        return this.data.newGoodsList
      }else{
        return []
      }
    },
    hotGoodsList:function(){
      if(typeof this.data.hotGoodsList=='object'){
        return this.data.hotGoodsList
      }else{
        return []
      }
    },
  },
  components: {
    tabBtn
  },
  async mounted(){
    // console.log(this)
    //console.log(api)
    let res = await axios.get(api.IndexUrl)
    // console.log(res.data)
    this.data = res.data.data
    //console.log(this.data)
    //console.log(this.data.brandList)
    
  },
  
}
</script>
<style lang="less">
  #home{
    .van-grid-item{
      overflow: hidden;
    }
    .swiperimg{
      widows: 100vw;;
      height: 200px;
    }
    .brandlist{
      .van-grid-item__content{
        padding: 0;
      }
      .van-image{
        border: 2px solid #ffffff;
      }
      .name{
        position: absolute;
        top: -5px;
        left: 10px;
      }
      .price{
        position: absolute;
        top: 20px;
        left: 10px;
        font-size: 14px;
        color: #999;
      }
    }
    .newgoodslist{
      
      .name{
        margin:0px;
        width: 90%;
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
      }
      .price{
        margin: 0;
        font-size: 14px;
        color: red;
      }

    }
    .hotlist{
      .van-card__price-integer{
        color: red;
      }
      .van-card{
        background-color: #ffffff;
      }
    }
  }
</style>
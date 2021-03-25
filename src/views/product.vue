<template>
    <div>
        <van-nav-bar
            title="商品详情"
            left-text="返回"
            left-arrow
            @click-left="onClickLeft"
        />
        <van-swipe :autoplay="3000">
            <van-swipe-item v-for="(image, index) in images" :key="index">
                <img class="img-detail" v-lazy="image.img_url" />
            </van-swipe-item>
        </van-swipe>
        <div class='info'>
            <span>30天无忧退货</span>
            <span>48小时快速退款</span>
            <span>满88元免邮费</span>
        </div>

        <div class="goods-detail">
            <h3>{{info.name}}</h3>
            <h5>{{info.goods_brief}}</h5>
            <p>￥{{info.retail_price}}</p>
        </div>
        <van-cell is-link @click="showPopup">请选择规格数量</van-cell>
        <van-popup v-model="show" position="bottom" :style="{ height: '30%' }" />

        <div class="proParams">
            <h3>商品参数</h3>
            <div class="proItem" v-for="(attribute,index) in goodsDetail.attribute" :key="index">
                <span class="title"> {{attribute.name}} </span>
                <span class="value">{{attribute.value}}</span>
            </div>
        </div>

        <div class="proDetail" v-html="info.goods_desc">

        </div>

        <van-goods-action>
            <van-goods-action-icon icon="cart-o" text="购物车" />
            <van-goods-action-icon icon="star" text="已收藏" color="#ff5000" />
            <van-goods-action-button type="warning" @click="addOrBuy" text="加入购物车" />
            <van-goods-action-button type="danger" @click="addOrBuy" text="立即购买" />
        </van-goods-action>
        <van-sku
            v-model="showSku"
            :sku="sku"
            :goods="goods"
            
            @buy-clicked="onBuyClicked"
            @add-cart="onAddCartClicked"
        />
    </div> 
</template>

<script>
import axios from 'axios'
import { Toast } from 'vant';
import { Lazyload } from 'vant';

let sku = {
    tree: [
    {
      k: '颜色', // skuKeyName：规格类目名称
      k_s: 's1', // skuKeyStr：sku 组合列表（下方 list）中当前类目对应的 key 值，value 值会是从属于当前类目的一个规格值 id
      v: [
        {
          id: '1', // skuValueId：规格值 id
          name: '红色', // skuValueName：规格值名称
        },
        {
          id: '2',
          name: '蓝色',
        }
      ],
      largeImageMode: true, //  是否展示大图模式
    }
  ],
//   所有 sku 的组合列表，比如红色、M 码为一个 sku 组合，红色、S 码为另一个组合
  list: [
    {
      id: 2259, // skuId
      's_1': '1', // 规格类目 k_s 为 s1 的对应规格值 id
      's_2': '3', // 规格类目 k_s 为 s2 的对应规格值 id
      price: 259800, // 价格（单位分）
      stock_num: 110 // 当前 sku 组合对应的库存
    },
    {
      id: 2259, // skuId
      's_1': '1', // 规格类目 k_s 为 s1 的对应规格值 id
      's_2': '4', // 规格类目 k_s 为 s2 的对应规格值 id
      price: 259800, // 价格（单位分）
      stock_num: 110 // 当前 sku 组合对应的库存
    },
    {
      id: 2259, // skuId
      's_1': '1', // 规格类目 k_s 为 s1 的对应规格值 id
      's_2': '5', // 规格类目 k_s 为 s2 的对应规格值 id
      price: 259800, // 价格（单位分）
      stock_num: 110 // 当前 sku 组合对应的库存
    },
    {
      id: 2259, // skuId
      's_1': '2', // 规格类目 k_s 为 s1 的对应规格值 id
      's_2': '3', // 规格类目 k_s 为 s2 的对应规格值 id
      price: 259800, // 价格（单位分）
      stock_num: 110 // 当前 sku 组合对应的库存
    },
    {
      id: 2259, // skuId
      's_1': '2', // 规格类目 k_s 为 s1 的对应规格值 id
      's_2': '4', // 规格类目 k_s 为 s2 的对应规格值 id
      price: 259800, // 价格（单位分）
      stock_num: 110 // 当前 sku 组合对应的库存
    },
    {
      id: 2259, // skuId
      's_1': '2', // 规格类目 k_s 为 s1 的对应规格值 id
      's_2': '5', // 规格类目 k_s 为 s2 的对应规格值 id
      price: 259800, // 价格（单位分）
      stock_num: 110 // 当前 sku 组合对应的库存
    },
  ],
  price: '1.00', // 默认价格（单位元）
  stock_num: 227, // 商品总库存
  collection_id: 2261, // 无规格商品 skuId 取 collection_id，否则取所选 sku 组合对应的 id
  none_sku: false, // 是否无规格商品
  
  hide_stock: false // 是否隐藏剩余库存

}


export default {
    props:['id'],
    data() {
        return {
            goodsDetail:{},
            info:"",
            show: false,
            showSku:false,
            sku:sku,
            goods: {
                title:'测试',
                // 默认商品 sku 缩略图
                picture: 'https://img.yzcdn.cn/1.jpg'
    }
        }
    },

    async created() {
        let Goods = await axios.get(this.$root.api.GoodsDetail,{params:{id:this.id}})
        this.goodsDetail = Goods.data.data
        this.info = this.goodsDetail.info
        // console.log(this.goodsDetail)
        console.log(this.goodsDetail)
        this.goods.picture = this.info.primary_pic_url
        this.goods.title = this.info.name
        this.sku.price = this.info.retail_price
        this.sku.stock_num = this.info.goods_number
        let tree =[]
        this.goodsDetail.specificationList.forEach((item,index) => {
            let arr = []
            item.valueList.forEach((product,i) => {
                arr.push({
                    id:product.id,
                    name:product.value
                })
            })
            tree.push({ 
                k: item.name, // skuKeyName：规格类目名称
                k_s: 's_'+item.specification_id, // skuKeyStr：sku 组合列表（下方 list）中当前类目对应的 key 值，value 值会是从属于当前类目的一个规格值 id
                v: arr
            })
        });
        this.sku.tree = tree
    },
    computed: {
        images:function(){
            if (this.goodsDetail.gallery==undefined){
                return []
            }else{
                return this.goodsDetail.gallery
            }
        }
    },
    methods: {
        onClickLeft() {
            this.$router.go(-1)
        },
        showPopup() {
            this.show = true;
        },
        onBuyClicked(){
            // 购买
        },
        onAddCartClicked(skuData){
            // 加入购物车
            console.log(skuData)
            let chooseContent = skuData.selectedSkuComb['s_1']+'_'+ skuData.selectedSkuComb['s_2']
            let resultPro = this.goodsDetail.productList.filter((item,index)=>{
                if(item.goods_specification_ids==chooseContent){
                    return true
                }else{
                    return false
                }
            })
            console.log(resultPro)
            this.showSku=false
        },
        addOrBuy:function(){
            this.showSku=true
        }
  },

}
</script>

<style lang="less">
    .img-detail{
        width: 100%;
    }
    .info{
        width: 100%;
        display: flex;
        justify-content: space-around;
        font-size: 12px;
        color:#999;
        height: 24px;
        line-height: 24px;
        background: #efefef;
        span{
            position: relative;
        }
        span::before{
            content: "";
            display: block;
            position: absolute;
            left: -10px;
            top: 8px;
            width: 4px;
            height: 4px;
            border-radius: 4px;
            border: 1px solid red;
            }
        
        }
    .goods-detail{
            h5{
                margin: 0;
                color:#999;
            }
            p{
                margin-top: 30px 0 30px 0; 
                color:red;
            }
        }
    .proParams{
        padding: 20px 0;
        h3{
                margin: 12px auto;
            }
        .proItem{
            width: 95%;
            margin: 0 10px;
            display: flex;
            justify-content: space-between;
            height: 24px;
            font-size: 12px;
            background: #efefef;
            line-height: 24px;
            text-align: center;
            border: 0.1px solid #ffffff;
            .title{
                width: 20%;
                padding:0 10px;
                border-right: 1px solid #ffffff;
            }
           .value{
               margin: 0 10px;
                width: 80%;
                overflow: hidden;
                text-overflow: ellipsis;
                // text-align: center;
                white-space: nowrap;
            }
        }
    }
    .proDetail{
            width: 100%;
            img{
                width: 100%;
            }
            p{
                margin: 0;
                display: flex;
            }

        }
    
    
</style>
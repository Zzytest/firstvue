<template>
    <div id="categoryList">
        <van-nav-bar
            title="产品分类列表"
            left-text="返回"
            left-arrow
            @click-left="onClickLeft"
        />
        
        <van-tabs v-model="tabActive">
            <van-tab v-for="(brothercateList,index) in category" 
            :key="index" 
            :title="brothercateList.name">
            <h3>{{brothercateList.name}}</h3>
            <p>{{brothercateList.front_name}}</p>
            <div v-if="brothercateList.plist">
            <van-grid :border="false" :column-num="2" :gutter="10">
                
                    <van-grid-item v-for="(cateplist,index1) in brothercateList.plist.data" 
                    :key="index1" :to="'/product/'+cateplist.id">
                        <van-image :src="cateplist.list_pic_url" />
                        <div class="name">
                            
                            <h4>{{cateplist.name}}</h4>
                            <p>￥{{cateplist.retail_price}}</p>
                        </div>
                        
                    </van-grid-item>
                
                
                
            </van-grid>
            </div>
            </van-tab>
            
            
        </van-tabs>
    </div>
</template>

<script>
import axios from 'axios'
import { AddressDetail } from '../../../mall/src/assets/config/api'

export default {
    props:['id'],
    data() {
        return {
            tabActive:0,
            category:[]
        }
    },
    async created() {
        
        let res = await axios.get(this.$root.api.GoodsCategory,{params:{id:this.id}})
        this.category = res.data.data.brotherCategory
        // console.log(this.category
        let id = this.category[0].id;
        
        //this.getlist(id,1)
        this.category.forEach(async (brothercateList,index) => {
            brothercateList.plist=await this.getlist(brothercateList.id,1)
            // console.log(brothercateList.plist)
            this.$forceUpdate()//强制更新
        });
        },
    methods: {
        async getlist(cid,page){
            let res = await axios.get(`${this.$root.api.GoodsList}?categoryId=${cid}&page=${page}&size=20`)
            //console.log(res)
            return res.data.data
        },
        onClickLeft:function(){
            this.$router.go(-1)
        }

    },
}
</script>

<style lang="less">
#categoryList{
    
    van-tab__pane{
        background-color:#f9f8fd;
    }
    .van-grid{
        .van-grid-item{
           
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
            .name{
                width: 100%;
                h4{
                overflow: hidden;
                white-space: nowrap;
                margin: 0;
                text-overflow:ellipsis;
            }
            p{
                margin: 0;
                color: coral;
            }
            
            }
        }
    }
}
    
</style>
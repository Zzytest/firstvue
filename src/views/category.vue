<template>
    <div id="catgory">
        <h1>categore</h1>
        <van-search v-model="value" placeholder="商品搜索 共239亿款好物" input-align='center' />
        <van-tree-select height="80vh" 
        :items="items" 
        :main-active-index.sync="active"
        @click-nav="changeRight"
        >
            <template slot="content">
                <img src="" alt="">
                <van-grid :column-num="3">
                    <van-grid-item 
                    v-for="(sublist,index) in subCategoryList" 
                    :key="index" 
                    :icon="sublist.wap_banner_url" 
                    :text="sublist.name"
                    :to="'/categoryList/'+sublist.id"
                     />
                </van-grid>
            </template>
        </van-tree-select>
        
        
        <tab-btn></tab-btn>
    </div>
</template>

<script>
import axios from 'axios'
import api from '@/assets/config/api.js'
import tabBtn from '@/components/tabBtn'

export default {
    data(){
        return{
            categorylist:{},
            value:'',
            activeKey: 0,
            active: 0,
            subCategoryList:[]
            
        }
    },
    components:{
        tabBtn
    },
    async created(){
        let res = await axios.get(api.CatalogList)
        this.categorylist = res.data.data;
        
        // console.log(res.data)
    },
    computed: {
        items:function(){
            if(this.categorylist.categoryList==undefined){
                return []
            }else{
                let arr = []
                this.categorylist.categoryList.forEach((item,index) => {
                    item.text = item.name
                    arr.push(item)
                })
                return arr
                ;
            }
        },
        
    },
    watch: {
        active:async function(){
            if(this.items.length!==0){
                let id = this.items[this.active].id
                // console.log(id)
                let res = await axios.get(api.CatalogCurrent,{params:{id}})
                // console.log(res.data)
                this.subCategoryList = res.data.data.currentCategory.subCategoryList
                // console.log(this.subCategoryList)
                
            }else{
                this.subCategoryList=[]
            }
            
        }
    },
    methods: {
        changeRight:function(index){
            // console.log(index)
            //console.log(this.active)
            this.active=index
        }
    },
    mounted() {
        
    },
}
</script>

<style lang="less">
    // .category{
    //     van-tree-select{

    //     }
    // }
</style>
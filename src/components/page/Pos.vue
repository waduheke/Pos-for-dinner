<template lang="html">
  <div class="pos">
    <div>
      <el-row>
        <el-col :span='7' class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐" class="first-bar">
            <!-- 出现一个左边距无法推开的鬼屎问题！ -->
            <el-table :data="tableData" border style="width:100%">
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <el-table-column label="操作" width="100" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" @click="delSingleGoods(scope.row)" size="small">删除</el-button>
                  <el-button type="text" @click="addOrderList(scope.row)" size="small">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
          <div>
            <el-button class="sub-btn" type="warning" >挂单</el-button>
            <el-button class="sub-btn" @click="delAllGoods()" type="danger" >删除</el-button>
            <el-button class="sub-btn" @click="getAllMoney()"type="success" >结账</el-button>
          </div>
          </el-tab-pane>
          <el-tab-pane label="挂单"></el-tab-pane>
          <el-tab-pane label="外卖"></el-tab-pane>
        </el-tabs>
        </el-col>
        <el-col :span="15">
          <div class="often-goods">
            <div class="title">
              常用商品
            </div>
            <div class="often-goods-list">
              <ul class="clearfix">
                <li v-for="item in oftenGoods">
                  <span @click="addOrderList(item)">{{item.goodsName}}</span>
                  <span class="o-price">￥{{item.price}}元</span>
                </li>
              </ul>
            </div>
          </div>
          <div class="goods-type">
            <el-tabs>
              <el-tab-pane label="汉堡">
                <ul class="cooklist clearfix">
                  <li v-for="goods in type0Goods" @click="addOrderList(goods)" class="clearfix">
                    <span  class="foodImg"><img :src="goods.foodImg" width="100%"></img></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">{{goods.price}}</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="饮料">
                <ul class="cooklist clearfix">
                  <li v-for="goods in type1Goods" @click="addOrderList(goods)" class="clearfix">
                    <span  class="foodImg"><img :src="goods.foodImg" width="100%"></img></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">{{goods.price}}</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="小吃">
                <ul class="cooklist clearfix">
                  <li v-for="goods in type2Goods" @click="addOrderList(goods)" class="clearfix">
                    <span  class="foodImg"><img :src="goods.foodImg" width="100%"></img></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">{{goods.price}}</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="套餐">
                <ul class="cooklist clearfix">
                  <li v-for="goods in type3Goods" @click="addOrderList(goods)" class="clearfix">
                    <span  class="foodImg"><img :src="goods.foodImg" width="100%"></img></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">{{goods.price}}</span>
                  </li>
                </ul>
              </el-tab-pane>
            </el-tabs>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Pos',
  created:function(){
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
    .then(response=>{
      console.log(response);
      this.oftenGoods=response.data;
    })
    .catch(error=>{
      console.log(error);
      alert('网络错误，无法获取')
    });
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
    .then(response=>{
      console.log(response);
      this.type0Goods=response.data[0];
      this.type1Goods=response.data[1];
      this.type2Goods=response.data[2];
      this.type3Goods=response.data[3];
    })
  },
  mounted: function(){
    var orderHeight = document.body.clientHeight;
    document.getElementById('order-list').style.height = orderHeight + 'px';
  },
  methods:{
    addOrderList(goods){
      this.totalCount = 0;
      this.totalMoney = 0;
      let isHave = false;
      for (let i=0; i<this.tableData.length;i++){
            if(this.tableData[i].goodsId==goods.goodsId){
              isHave=true; //存在
              }
          }
      if(isHave){
        let arr = this.tableData.filter(o =>o.goodsId == goods.goodsId);
        arr[0].count++;
      }
      else {
        let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
        this.tableData.push(newGoods);
      }

      this.tableData.forEach(element=>{
        this.totalCount+=element.count;
        this.totalMoney+=(element.count*element.price);
      })
    },
    delSingleGoods(goods){

      this.tableData=this.tableData.filter(o=>o.goodsId!=goods.goodsId);
    },
    delAllGoods(){
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
    },
    getAllMoney(){
      this.totalCount = 0;
      this.totalMoney = 0;
      if(this.tableData){
        this.tableData.forEach(element=>{
          this.totalCount += element.count;
          this.totalMoney += this.totalMoney+(element.totalCount*element.price);
          alert('你所消费的金额是'+this.totalMoney);
        })
      }
      alert('你所消费的金额是'+this.totalMoney);
    }
  },
  data(){
    return {
      tableData:[],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
      totalCount:0,
      totalMoney: 0,
    }
  }
}
</script>

<style lang="css">
  .pos {
    width:100%;
  }
  .pos-order {
    background-color: #F9FAFC;
    border-right: 1px solid #c7c7c7;
  }
  .sub-btn {
    margin: 0 7%;
    margin-top: 30px;
  }
  .title{
      height: 20px;
      border-bottom:1px solid #D3DCE6;
      background-color: #F9FAFC;
      padding:10px;
  }
  .often-goods-list ul li{
     list-style: none;
     float:left;
     border:1px solid #E5E9F2;
     padding:10px;
     margin:5px;
     background-color:#fff;
  }
 .o-price{
     color:#58B7FF;
  }
  .clearfix:after{
   content: "020";
   display: block;
   height: 0;
   clear: both;
   visibility: hidden;
}
 .clearfix {
   /* 触发 hasLayout */
   zoom: 1;
}
.cooklist li{
  list-style:none;
  width:23%;
  border: 1px solid #E5E9F2;
  height: auto;
  overflow:hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
}

.coolist li span {
  display:block;
  float: left;
}

.foodImg {
  width:40%;
}

.foodName {
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}

.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
</style>

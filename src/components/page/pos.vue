<template>
  <div id="pos">
      <div>
            <div style="width:40%;float:left;">
                <el-table :data="goodsInfo" >
                    <el-table-column prop='goodsName' label='商品'></el-table-column>
                    <el-table-column prop='count' label='数量'></el-table-column>
                    <el-table-column prop='price' label='价格'></el-table-column>
                    <el-table-column label='操作' fixed="right">
                        <template scope="scope">
                            <el-button type="text" size="small" @click="delGoods(scope.row)">删除</el-button>
                            <el-button type="text" size="small" @click="addGoodsList(scope.row)">增加</el-button>
                        </template>
                    </el-table-column>
                </el-table>
                <div>
                    <span>数量 :{{totalCount}}</span>
                    <span>合计 :{{totalMoney}}</span>
                </div>
            </div>
          <ul class="goodsList" style="width:60%;flaot:left;overflow:hidden;">
              <li v-for ="goods in oftenGoods" @click = 'addGoodsList(goods)'>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">{{goods.price}}</span>
              </li>
          </ul>
      </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
    name: 'pos',
    data (){
        return {
            goodsInfo :[],
            oftenGoods:[],
            totalCount:0,
            totalMoney:0
        }
    },
    created :function(){
        axios.get("https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods")
        .then(response=>{
            // console.log(response);
                this.oftenGoods = response.data;
        }).catch(err=>{
            console.log("11111");
                
        })
    },
    methods: {
        addGoodsList(goods){
            let isHave = false;
            this.totalCount = 0;
            this.totalMoney = 0;
            //判断是否存在商品
            for(let i=0; i<this.goodsInfo.length; i++){
                // console.log(len[i].goodsId);
                if(this.goodsInfo[i].goodsId==goods.goodsId){
                    isHave=true;
                }
            }
            if(isHave){
                let arr = this.goodsInfo.filter(o=>o.goodsId==goods.goodsId)
                arr[0].count++;
            }else{
                let newGoods = {
                    goodsId:goods.goodsId,
                    goodsName:goods.goodsName,
                    price:goods.price,
                    count:1
                    };
                this.goodsInfo.push(newGoods);
            }

            this.goodsInfo.forEach(element => {
                this.totalCount +=element.count;
                this.totalMoney =this.totalMoney + (element.price*element.count)
            });
        },
        delGoods(goods){
            this.goodsInfo = this.goodsInfo.filter(o=>o.goodsId !=goods.goodsId);
            this.getAllMoney();
        },
        //汇总数量和金额
        getAllMoney(){
            this.totalCount=0;
            this.totalMoney=0;
            if(this.goodsInfo){
                this.goodsInfo.forEach(element => {
                    this.totalCount +=element.count;
                    this.totalMoney =this.totalMoney + (element.price*element.count)
                });
            }
        }
        

    },
}
</script>

<style>
  li{list-style: none}
  *{margin: 0;padding:0;}
.goodsList li{float: left;width: 50%;height: 30px;line-height: 30px;}
</style>

<template>
  <div class="hello">
    <!--
          使用element-ui 布局  一行 两列
        -->
    <el-row>
      <el-col :span='7' class="pos-order" id="order-list" >
        <!--订单栏-->
        <el-tabs  v-model="activeName">
          <el-tab-pane label="点餐" name="first">
            <!--点餐-->
            <el-table border style="width:100%" :data="tableData">
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width='80'></el-table-column>
              <el-table-column prop="price" label="金额" width='70'></el-table-column>
              <!--
                                fixed="right" 固定显示
                              -->
              <el-table-column label="操作" width='100'>
                <template scope="scope">
                  <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                  <!--
                                        scope.row  增加 某一行
                                      -->
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="totalDiv">
              <small>数量：</small>{{totalCount}} &nbsp;&nbsp;&nbsp;&nbsp;
              <small>价格：</small>{{totalMoney}}
            </div>
            <div class="btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="delAllGoods">删除</el-button>
              <el-button type="success" @click="checkout">结账</el-button>
            </div>
  
          </el-tab-pane>
          <el-tab-pane label="挂单" name="second">挂单</el-tab-pane>
          <el-tab-pane label="外卖" name="third">外卖</el-tab-pane>
  
        </el-tabs>
      </el-col>
  
      <el-col :span='17' class="often-goods">
        <!--商品栏-->
        <div class="title">常用商品</div>
  
        <div class="often-goods-list">
          <ul v-for="goods of oftenGoods" @click="addOrderList(goods)">
            <li>
              <span>{{goods.goodsName}}</span>
              <span class="o-price">￥{{goods.price}}元</span>
            </li>
          </ul>
        </div>
  
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <!--汉堡-->
              <ul class='cookList'>
                <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                  <span class="foodImg">
                    <img :src="goods.goodsImg" width="100%">
                  </span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <!--小食-->
              <ul class='cookList'>
                <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                  <span class="foodImg">
                    <img :src="goods.goodsImg" width="100%">
                  </span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <!--饮料-->
              <ul class='cookList'>
                <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                  <span class="foodImg">
                    <img :src="goods.goodsImg" width="100%">
                  </span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <!--套餐-->
              <ul class='cookList'>
                <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                  <span class="foodImg">
                    <img :src="goods.goodsImg" width="100%">
                  </span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
          </el-tabs>
        </div>
  
      </el-col>
    </el-row>
  
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'pos',
  data() {
    return {
      activeName:'first',
      tableData: [],// 订单列表
      oftenGoods: [],// 常用商品
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalCount: 0,// 总数量
      totalMoney: 0,// 总价格
    }
  },
  // element 是虚拟dom   解决高度不能100%
  // mounted 生命周期 表示 dom加载 完成后
  mounted: function () {
    var orderHeight = document.body.clientHeight;
    document.getElementById('order-list').style.height = orderHeight + 'px'
  },
  // 创建的时候
  created: function () {
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
      .then(res => {
        this.oftenGoods = res.data
      })
      .catch(error => {
        console.log(error)
        alert('不能访问')
      });
    //读取分类商品列表
    axios.get('http://jspang.com/DemoApi/typeGoods.php')
      .then(response => {
        //this.oftenGoods=response.data;
        this.type0Goods = response.data[0];
        this.type1Goods = response.data[1];
        this.type2Goods = response.data[2];
        this.type3Goods = response.data[3];

      })
      .catch(error => {
        alert('网络错误，不能访问');
      })
  },
  methods: {
    // 添加
    addOrderList(goods) {

      let isHave = false;// 默认 不存在

      //判断在订单列表中是否存在
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId === goods.goodsId) {
          isHave = true
        }
      }
      // 如果存在 数量+1
      if (isHave) {
        // 根据 goodsId 进行 过滤
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);// 返回 的是 数组
        arr[0].count++;
        // checkedGoods.
      } else {
        // 如果不存在 添加 到数组中
        let newGoods = { goodsId: goods.goodsId, goodsName: goods.goodsName, price: goods.price, count: 1 };
        this.tableData.push(newGoods);
      }

      this.getAllCount();// 添加 重新计算
    },
    // 删除
    delSingleGoods(goods) {
      this.tableData = this.tableData.filter(g => g.goodsId != goods.goodsId);
      this.getAllCount();// 删除重新计算
    },
    delAllGoods(goods) {
       this.tableData = [];
       this.totalCount = 0;
       this.totalMoney = 0;
    },
    // 汇总 数量 金额
    getAllCount() {
      // 添加 之前 先将 总价 总数量清零
      this.totalCount = 0;
      this.totalMoney = 0;
      if (this.tableData) {
        //进行 数量 价格 合算
        this.tableData.forEach(ele => {
          this.totalCount += ele.count;
          this.totalMoney = this.totalMoney + (ele.count * ele.price);
        })
      }
    },
    // 模拟结账
    checkout(){
      if(this.totalCount != 0){
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        // element -ui 提醒
        this.$message({
          message: '恭喜你，这是一条成功消息',
          type: 'success'
        })
      }else{
        this.$message.error('不能空结。老板了解你急切的心情！');
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.totalDiv {
  padding: 10px;
  border-bottom: 1px solid #D3DCE6;
}



/*高不能 100%*/

.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #C0CCDA;
}

.btn {
  margin-top: 10px;
}

.title {
  height: 21px;
  border-bottom: 1px solid #D3DCE6;
  background-color: #F9FAFC;
  padding: 10px;
  text-align: left
}

.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #E5E9F2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
}

.o-price {
  color: #58B7FF;
}

.goods-type {
  clear: both
}

.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #E5E9F2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
}

.cookList li span {
  display: block;
  float: left;
}

.foodImg {
  width: 40%;
}

.foodName {
  font-size: 16px;
  padding-left: 10px;
  color: brown;
}

.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
</style>

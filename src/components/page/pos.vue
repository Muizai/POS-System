<template>
    <div>
        <el-row>
            <el-col :span="7" class="orderList" id="orderListId">
                <el-tabs>
                    <el-tab-pane label="点餐">
                        <el-table :data="tableData" border class="">
                            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                            <el-table-column prop="count" width="50" label="数量"></el-table-column>
                            <el-table-column prop="price" width="70" label="金额"></el-table-column>
                            <el-table-column width="150" fixed="right" label="操作">
                                <template scope="scope">
                                    <el-button type="text" size="small" @click="reduceGoods(scope.row)">减少</el-button>
                                    <el-button type="text" size="small" @click="addGoods(scope.row)">增加</el-button>
                                    <el-button type="text" size="small" @click="delSingle(scope.row)">删除</el-button>
                                </template>

                            </el-table-column>
                        </el-table>
                        <div class="count">
                            <small>数量：</small>{{totalCount}}
                            <small>金额：</small>{{totalMoney}}
                        </div>
                        <div class="div-btn">
                            <el-button type="danger" @click="delAll">删除</el-button>
                            <el-button type="warning" @click="guadan">挂单</el-button>
                            <el-button type="success" @click="check">结账</el-button>

                        </div>
                    </el-tab-pane>
                    <el-tab-pane label="挂单">
                        <el-table :data="tableData_guadan" border class="">
                            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                            <el-table-column prop="count" width="50" label="数量"></el-table-column>
                            <el-table-column prop="price" width="70" label="金额"></el-table-column>
                            
                        </el-table>
                        <div class="count">
                            <small>数量：</small>{{totalCount_guadan}}
                            <small>金额：</small>{{totalMoney_guadan}}
                        </div>
                        <div class="div-btn">
                            <el-button type="danger" @click="delAll_guadan">删除</el-button>
                            <el-button type="success" @click="check_guadan">结账</el-button>

                        </div>
                    </el-tab-pane>
                    <el-tab-pane label="外卖"></el-tab-pane>
                </el-tabs>
            </el-col>
            <el-col :span="17">
                <div class="title">常用商品</div>
                <div class="oftenList">
                    <ul>
                        <li v-for="item in oftenGoods" @click="addGoods(item)">
                            <span>{{item.goodsName}}</span>
                            <span>{{item.price}}</span>
                        </li>
                    </ul>
                </div>
                <div class="typeList">
                    <el-tabs>
                        <el-tab-pane label="主食">
                            <ul>
                                <li v-for="item in type0Goods" @click="addGoods(item)">
                                    <span><img :src="item.goodsImg" alt=""></span>
                                    <span>{{item.goodsName}}</span>
                                    <span>{{item.price}}</span>
                                </li>
                            </ul>
                        </el-tab-pane>
                        <el-tab-pane label="小食">
                            <ul>
                                <li v-for="item in type1Goods" @click="addGoods(item)">
                                    <span><img :src="item.goodsImg" alt=""></span>
                                    <span>{{item.goodsName}}</span>
                                    <span>{{item.price}}</span>
                                </li>
                            </ul>
                        </el-tab-pane>
                        <el-tab-pane label="饮料">
                            <ul>
                                <li v-for="item in type2Goods" @click="addGoods(item)">
                                    <span><img :src="item.goodsImg" alt=""></span>
                                    <span>{{item.goodsName}}</span>
                                    <span>{{item.price}}</span>
                                </li>
                            </ul>
                        </el-tab-pane>
                        <el-tab-pane label="套餐">
                            <ul>
                                <li v-for="item in type3Goods" @click="addGoods(item)">
                                    <span><img :src="item.goodsImg" alt=""></span>
                                    <span>{{item.goodsName}}</span>
                                    <span>{{item.price}}</span>
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
        data() {
            return {
                tableData: [],
                tableData_guadan:[],
                oftenGoods: [],
                type0Goods: [],
                type1Goods: [],
                type2Goods: [],
                type3Goods: [],
                totalCount: 0,
                totalMoney: 0,
                totalCount_guadan:0,
                totalMoney_guadan:0

            }
        },
        mounted: function () {
            let orderListHeight = document.body.clientHeight;
            document.getElementById("orderListId").style.height = orderListHeight + "px";
        },
        created: function () {
            axios.get("https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods")
                .then(response => {
                    this.oftenGoods = response.data;
                })
                .catch(error => {
                    alert("Error")
                })

            axios.get("https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods")
                .then(response => {
                    this.type0Goods = response.data[0];
                    this.type1Goods = response.data[1];
                    this.type2Goods = response.data[2];
                    this.type3Goods = response.data[3];
                    console.log(this.type0Goods);

                })
                .catch(error => {
                    alert("Error")
                })


        },
        methods: {
            sum: function () {
                this.totalCount = 0;
                this.totalMoney = 0;
                if (this.tableData) {
                    for (let i = 0; i < this.tableData.length; i++) {
                        this.totalCount += this.tableData[i].count;
                        this.totalMoney += this.tableData[i].count * this.tableData[i].price;
                    }
                }

            },
            addGoods: function (item) {
                this.totalCount = 0;
                this.totalMoney = 0;
                let isHave = false;
                for (let i = 0; i < this.tableData.length; i++) {
                    if (this.tableData[i].goodsName == item.goodsName) {
                        isHave = true;
                    }
                }
                if (isHave) {
                    let arr = this.tableData.filter(o => o.goodsName == item.goodsName);
                    arr[0].count++;
                } else {
                    let newGoods = { goodsId: item.goodsId, goodsName: item.goodsName, count: 1, price: item.price };
                    this.tableData.push(newGoods);
                }
                this.sum();
            },
            reduceGoods: function (item) {
                let arr = this.tableData.filter(o => o.goodsName == item.goodsName);
                arr[0].count == 1 ? this.tableData = this.tableData.filter(o => o.goodsName != item.goodsName) : arr[0].count--;
                this.sum();
            },
            delSingle: function (item) {
                let arr = this.tableData.filter(o => o.goodsName != item.goodsName);
                this.tableData = arr;
                this.sum();
            },
            delAll: function () {
                this.tableData = [];
                this.totalCount = 0;
                this.totalMoney = 0;
            },
            check: function () {
                if (this.tableData != 0) {
                    this.tableData = [];
                    this.totalCount = 0;
                    this.totalMoney = 0;
                    this.$message({
                        message: '结账成功！',
                        type: 'success'
                    })
                } else {
                    this.$message.error("顾客还没有点餐！")
                }
            },
            guadan: function () {
                this.tableData_guadan=this.tableData;
                this.totalCount_guadan=this.totalCount;
                this.totalMoney_guadan=this.totalMoney;
                this.tableData=[];
                this.totalCount=this.totalMoney=0;
            },
            delAll_guadan:function(){
                this.tableData_guadan=[];
                this.totalCount_guadan=this.totalMoney_guadan=0;
            },
            check_guadan:function(){
                if(this.tableData_guadan!=0){
                    this.tableData_guadan=[];
                    this.totalCount_guadan=this.totalMoney_guadan=0;
                    this.$message({
                        message:'结账成功',
                        type:'success'
                    })
                }else{
                    this.$message.error("顾客还没有点餐！");
                }
            }
        }
    }
</script>
<style>
    .title {
        height: 40px;
        line-height: 40px;
        text-align: left;

    }

    .orderList {
        border-right: 1px solid #ccc;
    }

    .oftenList ul {
        overflow: hidden;
    }

    .oftenList li {
        list-style: none;
        padding: 10px;
        margin: 10px;
        border: 1px solid #ccc;
        float: left;
        cursor: pointer;
    }

    .typeList ul {
        overflow: hidden;
    }

    .typeList li {
        list-style: none;
        padding: 10px;
        margin: 10px;
        float: left;
        cursor: pointer;
        border: 1px solid #ccc;
    }

    .count {
        padding: 10px;
        border: 1px solid #ccc;
    }

    .div-btn {
        margin-top: 50px;
    }
</style>
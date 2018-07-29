<template>
    <div>
        <el-row :gutter="20">
            <el-col :span="8">
                <el-row>
                    <el-col>
                        <el-card shadow="hover" class="mgb20">
                            <div class="user-info">
                                <img src="static/img/img.jpg" class="user-avator" alt="">
                                <div class="user-info-cont">
                                    <div class="user-info-name">{{name}}</div>
                                    <div>{{role}}</div>
                                </div>
                            </div>
                            <div class="user-info-list">上次登录时间：<span>2018-01-01</span></div>
                            <div class="user-info-list">上次登录地点：<span>北京</span></div>
                        </el-card>
                        <el-card shadow="hover">
                            <div slot="header" class="clearfix">
                                <span>技能使用统计</span>
                            </div>
                            天气
                            <el-progress :percentage="57.2" color="#42b983"></el-progress>
                            订餐
                            <el-progress :percentage="29.8" color="#f1e05a"></el-progress>
                            电台
                            <el-progress :percentage="11.9"></el-progress>
                            音乐
                            <el-progress :percentage="1.1" color="#f56c6c"></el-progress>
                        </el-card>
                    </el-col>
                </el-row>
            </el-col>
            <el-col :span="16">
                <el-card shadow="hover" :body-style="{ height: '430px'}">
                    <div slot="header" class="clearfix">
                        <span>我的技能</span>
                        <el-button style="float: right; padding: 3px 0" type="text" @click="addItem">添加</el-button>
                    </div>
                    <el-table :data="botList" style="width: 100%" max-height="250">
                        <el-table-column prop="name" label="名字" width="150"></el-table-column>
                        <el-table-column prop="type" label="领域" width="150"></el-table-column>
                        <el-table-column prop="description" label="描述" width="450"></el-table-column>
                        <el-table-column fixed="right" label="删除" width="100"> 
                        <template slot-scope="scope">
                           <i class="el-icon-delete" @click="deleteItem(scope.$index,botList)"></i>
                        </template>
                        </el-table-column>
                        <el-table-column fixed="right" label="运行" width="100"> 
                        <template slot-scope="scope">
                           <i class="el-icon-circle-check-outline" @click="runItem(scope.$index,botList)"></i>
                        </template>
                        </el-table-column>
                    </el-table>
                </el-card>

            </el-col>
        </el-row>
    </div>
</template>

<script>
    export default {
        name: 'dashboard',
        data() {
            return {
                name: localStorage.getItem('ms_username'),
                botList: [
                ],
                i:''
            }
        },
        computed: {
            role() {
                return this.name === 'admin' ? '超级管理员' : '普通用户';
            },
        },
        mounted(){
                this.$http.post(this.HOST+'/searchRobotInfo/', {
                    owner: this.name
                }, {
                    emulateJSON: true
                })
                .then((response) => {
                    console.log(response.body)
                    if(response.body.retCode== 200) {
                        var i=0;
                        for(i=0;i<response.body.bot.length;i++){
                            let obj = {
                                id:response.body.bot[i].id,
                                name:response.body.bot[i].name,
                                type:response.body.bot[i].type,
                                description:response.body.bot[i].description
                            }
                            this.botList.push(obj)
                        }
                    } else {
                        this.$message.error("登陆失败，请重试."+"原因:"+response.body.reason);
                    }
                }, (response) => {
                    alert("响应失败！");
                    this.$message({
                        type: 'error',
                        message: response.message
                    });
                });
           
        },
        methods: {
            addItem(){
                //页面跳转到新建机器人
                this.$router.push('/form');
            },
            deleteItem(index, rows) {
                //删除数据库
                this.$http.post(this.HOST+'/deleteRobotInfo/', {
                    id: this.botList[index].id
                }, {
                    emulateJSON: true
                })
                .then((response) => {
                    console.log(response.body)
                    if(response.body.retCode == 200) {
                        this.$message.success('删除成功！');
                        //删除界面上的条目
                        rows.splice(index, 1);
                    } else {
                        this.$message.error("登陆失败，请重试."+"原因:"+response.body.reason);
                    }
                }, (response) => {
                    alert("响应失败！");
                    this.$message({
                        type: 'error',
                        message: response.message
                    });
                });
            },
            runItem(index,rows){

            },
        },
    }

</script>


<style scoped>
    .el-row {
        margin-bottom: 20px;
    }

    .grid-content {
        display: flex;
        align-items: center;
        height: 100px;
    }

    .grid-cont-right {
        flex: 1;
        text-align: center;
        font-size: 12px;
        color: #999;
    }

    .grid-num {
        font-size: 30px;
        font-weight: bold;
    }

    .grid-con-icon {
        font-size: 50px;
        width: 100px;
        height: 100px;
        text-align: center;
        line-height: 100px;
        color: #fff;
    }

    .grid-con-1 .grid-con-icon {
        background: rgb(45, 140, 240);
    }

    .grid-con-1 .grid-num {
        color: rgb(45, 140, 240);
    }

    .grid-con-2 .grid-con-icon {
        background: rgb(100, 213, 114);
    }

    .grid-con-2 .grid-num {
        color: rgb(45, 140, 240);
    }

    .grid-con-3 .grid-con-icon {
        background: rgb(242, 94, 67);
    }

    .grid-con-3 .grid-num {
        color: rgb(242, 94, 67);
    }

    .user-info {
        display: flex;
        align-items: center;
        padding-bottom: 20px;
        border-bottom: 2px solid #ccc;
        margin-bottom: 20px;
    }

    .user-avator {
        width: 120px;
        height: 120px;
        border-radius: 50%;
    }

    .user-info-cont {
        padding-left: 50px;
        flex: 1;
        font-size: 14px;
        color: #999;
    }

    .user-info-cont div:first-child {
        font-size: 30px;
        color: #222;
    }

    .user-info-list {
        font-size: 14px;
        color: #999;
        line-height: 25px;
    }

    .user-info-list span {
        margin-left: 70px;
    }

    .mgb20 {
        margin-bottom: 20px;
    }

    .todo-item {
        font-size: 14px;
    }

    .todo-item-del {
        text-decoration: line-through;
        color: #999;
    }

</style>

<template>
    <div>
        <el-col :span="12">
            <div class="crumbs" style="margin-right:20px;">
                <el-breadcrumb separator="/">
                    <el-breadcrumb-item><i class="el-icon-date"></i> 配置机器人</el-breadcrumb-item>
                    <el-breadcrumb-item>意图</el-breadcrumb-item>
                </el-breadcrumb>
            </div>
            <div class="container" style="margin-right:20px;">
                <div>
                    <img src="static/img/plus.png" style="width:20px;height:20px">
                    <span style="color: #606266;">输入 </span>
                </div>
                <div class="form-box" style="margin-top:20px">
                    <el-form ref="form" label-width="80px" >
                        <el-form-item label="意图">
                            <div v-for="(item,index) in arr1">
                                <el-input v-model="item.intent" style="width:60%;margin-bottom:7px" placeholder="如：greet"></el-input>
                                <img src="static/img/del.png" style="width:23px;height:23px;margin-top:5px;margin-left:5px;" @click="deletIntent(index)">
                            </div>
                        </el-form-item>
                    </el-form>
                    <div style="padding-bottom:20px;">
                        <el-button style="float:right; margin-right:20%" type="primary" @click="onSubmit1">提交</el-button>
                        <el-button style="float:right; margin-right:8%" type="primary" @click="addIntent">新增意图</el-button>
                    </div>
                </div>
            </div>
        </el-col>
         <el-col :span="12">
            <div class="crumbs" style="margin-left:20px;">
                <el-breadcrumb separator="/">
                    <el-breadcrumb-item><i class="el-icon-date"></i> 配置机器人</el-breadcrumb-item>
                    <el-breadcrumb-item>槽位</el-breadcrumb-item>
                </el-breadcrumb>
            </div>
            <div class="container" style="margin-left:20px;">
                <div>
                    <img src="static/img/plus.png" style="width:20px;height:20px">
                    <span style="color: #606266;">输入 </span>
                </div>
                <div class="form-box" style="margin-top:20px">
                    <el-form ref="form" label-width="80px" >
                        <el-form-item label="槽位">
                            <div v-for="(item,index) in arr2">
                                <el-input v-model="item.slot" style="width:60%;margin-bottom:7px" placeholder="如：place"></el-input>
                                <img src="static/img/del.png" style="width:23px;height:23px;margin-left:5px" @click="deletSlot(index)">
                            </div>
                        </el-form-item>
                    </el-form>
                    <div style="padding-bottom:20px;">
                         <el-button style="float:right; margin-right:20%" type="primary" @click="onSubmit2">提交</el-button>
                        <el-button style="float:right; margin-right:8%" type="primary" @click="addSlot">新增槽位</el-button>
                    </div>
                </div>
            </div>
        </el-col>
    </div>
</template>

<script>
    export default {
        name: 'baseform',
        data: function(){
            return {
                form1: {
                   
                },
                form2: {
                    
                },
                arr1: [
                    {intent:''}
                ],
                arr2: [
                    {slot:''}
                ],
            }
        },
        methods: {
            onSubmit1() {
                this.$http.post(this.HOST+'/intentInfo/', {
                    owner: localStorage.getItem('ms_username'),
                    name: this.arr1,
                }, {
                    emulateJSON: true
                })
                .then((response) => {
                    console.log(response.body)
                    if(response.body.retCode == 200) {
                        this.$message.success('提交成功！');
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
            onSubmit2() {
                this.$http.post(this.HOST+'/slotInfo/', {
                    owner: localStorage.getItem('ms_username'),
                    name: this.arr2,
                }, {
                    emulateJSON: true
                })
                .then((response) => {
                    console.log(response.body)
                    if(response.body.retCode == 200) {
                        this.$message.success('提交成功！');
                        //页面跳转
                        this.$router.push('/data');
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
            addIntent() {
                let obj = {
                    slot:'',
                    slotData:''
                }
                this.arr1.push(obj)
            },
            addSlot() {
                 let obj = {
                    slot:'',
                    slotData:''
                }
                this.arr2.push(obj)
            },
            deletIntent(index) {
                 this.arr1.splice(index,1)
            },
            deletSlot(index) {
                this.arr2.splice(index,1)
            }
        }
    }
</script>
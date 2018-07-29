<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 配置机器人</el-breadcrumb-item>
                <el-breadcrumb-item>基本信息</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="form-box">
                <el-form ref="form" :model="form" label-width="80px">
                    <el-form-item label="技能名称">
                        <el-input v-model="form.name"></el-input>
                    </el-form-item>
                    <el-form-item label="领域分类">
                        <el-input v-model="form.type"></el-input>
                    </el-form-item>
                    <el-form-item label="技能描述">
                        <el-input type="textarea" rows="5" v-model="form.description"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="onSubmit" style="float:right">提交</el-button>
                    </el-form-item>
                </el-form>
            </div>
        </div>

    </div>
</template>

<script>
    export default {
        name: 'baseform',
        data: function(){
            return {
                form: {
                    name: '',
                    type: '',
                    description: '',
                },
            }
        },
        methods: {
            onSubmit() {
                localStorage.setItem('domain',this.form.type);
                // 提交给后台
                this.$http.post(this.HOST+'/addRobotInfo/', {
                    name: this.form.name,
                    type: this.form.type,
                    description: this.form.description,
                    owner: localStorage.getItem('ms_username')
                }, {
                    emulateJSON: true
                })
                .then((response) => {
                    console.log(response.body)
                    if(response.body.retCode == 200) {
                        this.$message.success('提交成功！');
                        this.BOT=this.form.name;
                        //页面跳转
                        this.$router.push('/intent');
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
            }
        }
    }
</script>
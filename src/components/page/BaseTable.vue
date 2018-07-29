<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-warning"></i> 机器人测试</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
       
        <div class="container" style="width:500px;margin-top:30px;margin:0 auto">
            <div id="my-botui-app" style="height:500px !important">
                <bot-ui></bot-ui>
            </div>
        </div>
        <div style="float:right;margin-right:400px;margin-top:10px">
            <el-button  @click="deliverData">deliver</el-button>
            <el-button  @click="init">chat</el-button>
            <el-button  @click="clearChat">clear</el-button>
        </div>
    </div>
</template>

<script>
    import Vue from 'vue'
    import BotUI from 'botui'
    export default {
        data: function(){
            return {}
        },
        methods:{
            clearChat(){
                $("#my-botui-app").html("<bot-ui></bot-ui>");
            },
            deliverData(){
                    this.$http.post(this.HOST+'/deliverInfo/', {
                        owner: localStorage.getItem('ms_username'),
                        domain: localStorage.getItem('domain')
                    }, {
                        emulateJSON: true
                    })
                    .then((response) => {
                        console.log(response.body)
                        if(response.body.retCode == 200) {
                            this.$message.success('上传语料成功！');
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
            init(){
                const botui = BotUI('my-botui-app', {
                    vue: Vue // pass the dependency.
                })
                // $('.scroll-pane').jScrollPane();
                this.chat(botui);
                 botui.message
                    .bot({
                        delay: 1000,
                        content: '请输入您的请求'
                    })
            },
            // chat(botui) {
            //     var self = this;//用来引用外面定义的变量和函数
            //         botui.action.text({
            //             delay: 1000,
            //             action: {
            //                 value: '',
            //                 placeholder: '请输入'
            //             }
            //         }).then(function (res) {
            //             botui.message.bot({
            //                 loading: true
            //             }).then(function (index) {
            //                     var flag='';
            //                     self.loadingMsgIndex = index;
            //                     $.ajax({  
            //                             type:'get',  
            //                             url : 'http://10.108.211.229:5557/api/',   
            //                             async: false,
            //                             data:{input: res.value},
            //                             dataType:"json",//jsonp跨域
            //                             // jsonpCallback: "?", 
            //                             success: function(data) {  
            //                                     if(data.status == 0){
            //                                         botui.message.update(index, {
            //                                             loading: false,
            //                                             content:"您是否是这个动作："+data.text+" ?"
            //                                         })
            //                                         self.chat(botui);
            //                                     }
            //                                     if(data.status == 1){
            //                                         botui.message.update(index, {
            //                                             loading: false,
            //                                             content:"bot : "+data.text
            //                                         })
            //                                         self.chat(botui);
            //                                     }
            //                                     if(data.status == 2){
            //                                         var i=0;
            //                                         botui.message.update(index, {
            //                                                 loading: false,
            //                                                 content: data.action[0]
            //                                         })
            //                                         for(i=1;i<data.action.length;i++){
            //                                             botui.message.add({
            //                                                 loading: false,
            //                                                 content: data.action[i]
            //                                             })
            //                                         }
            //                                        self.chat(botui);
            //                                     }
            //                             },  
            //                             error: function(XMLHttpRequest, textStatus, errorThrown) {
            //                                         console.log(XMLHttpRequest.status);
            //                                         console.log(XMLHttpRequest.readyState);
            //                                         console.log(textStatus);
            //                                     }, 
            //                     }); 
                            
            //             });

            //         })            
            // },
                 chat(botui) {
                var self = this;//用来引用外面定义的变量和函数
                    botui.action.text({
                        delay: 1000,
                        action: {
                            value: '',
                            placeholder: '请输入'
                        }
                    }).then(function (res) {
                        botui.message.bot({
                            loading: true
                        }).then(function (index) {
                                var flag='';
                                self.loadingMsgIndex = index;
                                $.ajax({  
                                        type:'get',  
                                        url : 'http://10.108.216.161:8000/run',   
                                        async: false,
                                        data:{input: res.value},
                                        dataType:"json",//jsonp跨域
                                        // jsonpCallback: "?", 
                                        success: function(data) { 
                                            console.log(data.text) 
                                            console.log(typeof(data.text)) 
                                            botui.message.update(index, {
                                                        loading: false,
                                                        content: data.text
                                            })
                                            var i=1;
                                            for(i=1;i<data.text.length;i++){
                                                botui.message.add({
                                                    loading: false,
                                                    content: data.text[i]
                                                })
                                            }
                                             self.chat(botui);
                                            
                                        },  
                                        error: function(XMLHttpRequest, textStatus, errorThrown) {
                                                    console.log(XMLHttpRequest.status);
                                                    console.log(XMLHttpRequest.readyState);
                                                    console.log(textStatus);
                                                }, 
                                }); 
                            
                        });

                    })            
            },
        }
    }
</script>

<style scoped>
h1{
    text-align: center;
    margin: 30px 0;
}
p{
    line-height: 30px;
    margin-bottom: 10px;
    text-indent: 2em;
}
.logout{
    color: #409EFF;
}
</style>
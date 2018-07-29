<template>
    <div>
        <el-col :span="13">
            <div class="crumbs" style="margin-left:20px;">
                <el-breadcrumb separator="/">
                    <el-breadcrumb-item><i class="el-icon-date"></i> 配置机器人</el-breadcrumb-item>
                    <el-breadcrumb-item>导入数据</el-breadcrumb-item>
                </el-breadcrumb>
            </div>
            <div class="container" style="margin-left:20px;">
                <div>
                    <img src="static/img/plus.png" style="width:20px;height:20px">
                    <span style="color: #606266;">输入 </span>
                </div>
                <div class="form-box" style="margin-top:20px">
                    <el-form ref="form" :model="form" label-width="80px">
                        <el-form-item label="语料">
                            <el-input v-model="form.name" style="width:80%"></el-input>
                        </el-form-item>
                        <el-form-item label="意图">
                                <el-radio-group v-model="form.intent">
                                    <el-radio  v-for="item in intentList" :label= "item" style="margin-left: 5px;"></el-radio>
                                </el-radio-group>
                        </el-form-item>
                        <el-form-item label="槽值对">
                            <div v-for="item in arr">
                                <el-col :span="8">
                                        <el-select v-model="item.slot" placeholder="请选择槽">
                                            <el-option v-for="child in item.list" :label= "child" :value="child"></el-option>
                                        </el-select>
                                </el-col>
                                <el-col class="line" :span="4">--</el-col>
                                <el-col :span="8">
                                    <el-input v-model="item.slotData" style="width:90%"></el-input>
                               </el-col>
                                <el-col :span="4">
                                    <img src="static/img/del.png" style="width:23px;height:23px;margin-top:2px" @click="deletSlot(index)">
                                </el-col>
                            </div>
                        </el-form-item>
                    </el-form>
                    <div style="padding-bottom:20px;">
                        <el-button style="float:right; margin-right:10%" type="primary" @click="onSubmit">提交</el-button>
                        <el-button style="float:right; margin-right:6%" type="primary" @click="addSlot">新增槽值对</el-button>
                    </div>
                </div>
            </div>
        </el-col> 
        <el-col :span="11">
            <div class="crumbs" style="margin-left:20px;">
                <el-breadcrumb separator="/">
                    <el-breadcrumb-item><i class="el-icon-date"></i> 配置机器人</el-breadcrumb-item>
                    <el-breadcrumb-item>上传json文件</el-breadcrumb-item>
                </el-breadcrumb>
            </div>
            <div  style="margin-left:20px;">
                <!-- <input type="file" name="file" id="file_upload" />
	            <input type="button" value="上传"  @click="ajaxUpload" /> -->
                <el-tabs type="border-card" style="padding-bottom:30px">
                    <el-tab-pane label="查看JSON样例"> 
                        <pre class="brush:javascript;">
                            {
                                "rasa_nlu_data": {
                                    "common_examples": [
                                    {
                                        "text": "how about modern european", 
                                        "intent": "inform", 
                                        "entities": [
                                        {
                                            "start": 10, 
                                            "end": 25, 
                                            "value": "modern european", 
                                            "entity": "cuisine"
                                        }
                                        ]
                                    }, 
                                    {
                                        "text": "thank you good bye", 
                                        "intent": "thankyou"
                                    },
                                    ],
                                    "entity_examples": [], 
                                    "intent_examples": [] 
                                }
                            }
                        </pre>
                    </el-tab-pane>
                    <el-tab-pane label="上传JSON文件">
                        <div style="margin-left:15%">
                            <el-upload
                            class="upload-demo"
                            drag
                            action="http://localhost:3001/users/file"
                            multiple>
                            <i class="el-icon-upload"></i>
                            <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
                            <!-- <div class="el-upload__tip" slot="tip">只能上传jpg/png文件，且不超过500kb</div> -->
                            </el-upload>
                        </div>
                    </el-tab-pane>
                </el-tabs>
            </div>
        </el-col>          
    </div>
</template>


<script>
    import botInfo from './BaseForm.vue'
    export default {
        name: 'baseform',
        data: function(){
            return {
                form: {
                    name:'',
                    intent: '',
                },
                arr:[
                    {slot:'',slotData:'',start:'',end:'',list:['']}
                ],
                intentList:[''],
                slotList:['']
            }
        },
        created(){
            this.$nextTick(() => {
                SyntaxHighlighter.all();
            })
        },
        mounted(){
            //加载intent
                this.$http.post(this.HOST+'/getIntentInfo/', {
                    owner: localStorage.getItem('ms_username')
                }, {
                    emulateJSON: true
                })
                .then((response) => {
                    console.log(response.body)
                    if(response.body.retCode== 200) {
                        var i=0;
                        for(i=0;i<response.body.intent.length;i++){
                                this.intentList[i]=response.body.intent[i].name;
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
                 //加载slot
                this.$http.post(this.HOST+'/getSlotInfo/', {
                    owner: localStorage.getItem('ms_username')
                }, {
                    emulateJSON: true
                })
                .then((response) => {
                    console.log(response.body)
                    if(response.body.retCode== 200) {
                        var i=0;
                        for(i=0;i<response.body.slot.length;i++){
                                this.slotList[i]=response.body.slot[i].name;
                        }
                        this.arr[0].list=this.slotList;
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
            onSubmit() {
                var i=0;
                for(i=0;i<this.arr.length;i++){
                    //槽值起始字母位（从0开始）
                    this.arr[i].start=this.form.name.indexOf(this.arr[i].slotData);
                    this.arr[i].end=this.arr[i].start+this.arr[i].slotData.length;
                }
                this.$http.post(this.HOST+'/dataInfo/', {
                    owner: localStorage.getItem('ms_username'),
                    domain: localStorage.getItem('domain'),
                    name: this.form.name,
                    intent:this.form.intent,
                    slotArr:this.arr
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
            addSlot() {
                let obj = {
                    slot:'',
                    slotData:'',
                    list: this.slotList
                }
                this.arr.push(obj)
            },
             deletSlot(index){
      	        this.arr.splice(index,1)
            },
            // ajaxUpload() {
            //     var fd = new FormData();
            //     fd.append('file', $("#file_upload")[0].files[0]);
            //     $.ajax({
            //         type : "post",
            //         url : "http://localhost:3001/users/file",
            //         async : false,
            //         processData : false, // 不处理数据
            //         contentType : false, // 不设置内容类型
            //         data : fd,
            //         dataType : 'text', //返回类型json、text
            //         success : function(data) {
            //             alert(111111)
            //             alert(data);
            //         }
            //     });
            // },

        }
    }
</script>
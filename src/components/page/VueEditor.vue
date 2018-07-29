<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 配置机器人</el-breadcrumb-item>
                <el-breadcrumb-item>动作函数</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <el-tabs type="border-card">
                 <el-tab-pane label="文件样例">
                    <pre class="brush:python;">
                        import web
                        urls = ("/.*", "hello")
                        app = web.application(urls, globals())
                        class hello:
                            def GET(self):
                                return'Hello, world!'
                        if __name__ == "__main__":
                            app.run()
                    </pre>
                </el-tab-pane>
                <el-tab-pane label="文件上传">
                    <el-form ref="form" :model="form1" label-width="80px" style="width:30%;margin-left:30%">
                        <el-form-item label="函数名称">
                                <el-input v-model="fucName1"></el-input>
                        </el-form-item>
                    </el-form>
                    <div style="width:40%;margin:0 auto">
                        <el-upload
                        class="upload-demo"
                        drag
                        action="http://localhost:3001/users/actionFile"
                        multiple>
                        <i class="el-icon-upload"></i>
                        <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
                        </el-upload>
                        <el-button type="primary" @click="onSubmit" style="float:right">提交</el-button>
                    </div>
                </el-tab-pane>
                <el-tab-pane label="在线编辑">
                    <el-form ref="form" :model="form2" label-width="80px">
                        <el-form-item label="函数名称">
                            <el-input v-model="fucName2" style="width:40%"></el-input>
                        </el-form-item>
                    </el-form>
                    <quill-editor ref="myTextEditor" v-model="content" :options="editorOption"></quill-editor>
                    <el-button class="editor-btn" type="primary" @click="submit" style="float:right">提交</el-button>
                </el-tab-pane>
            </el-tabs>
        </div>
    </div>
</template>

<script>
    import 'quill/dist/quill.core.css';
    import 'quill/dist/quill.snow.css';
    import 'quill/dist/quill.bubble.css';
    import { quillEditor } from 'vue-quill-editor';
    export default {
        name: 'editor',
        data: function(){
            return {
                content: '',
                editorOption: {
                    placeholder: 'Hello World'
                },
                form1: {
                   funcName1: '',
                },
                form2: {
                   funcName2: '',
                }
            }
        },
        components: {
            quillEditor
        },
        methods: {
            onEditorChange({ editor, html, text }) {
                this.content = html;
            },
            submit(){
                console.log(this.content);
                this.$message.success('提交成功！');
            }
        }
    }
</script>
<style scoped>
    .editor-btn{
        margin-top: 20px;
    }
</style>
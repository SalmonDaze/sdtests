<template>
    <div class='container'>
        <vaild-form title='修改资料' :formHeight='600'>
            <template slot='body' style='float:'>
                <div style='text-align:left;margin-left:180px;'>
                    <div style='margin-top:30px;'>
                        <span>用户名：</span><el-input
                            :placeholder="name"
                            :disabled="true" style='width:150px;'>
                            </el-input>
                            <p style='margin-top:20px;'>
                                <span>UID：</span>{{UID}}
                            </p>
                            <p style='margin-top:20px;'>
                                <span>注册日期：</span>{{createAt.slice(0,createAt.indexOf('T'))}}
                            </p>
                            <p style='margin-top:20px;'>
                                <span>当前用户组：</span>{{ admin ? '管理员' : '普通用户'}}
                            </p>
                            <div class='sex_group'>
                                <span>请选择性别：</span>
                                <el-radio v-model="sex" label="male">男</el-radio>
                                <el-radio v-model="sex" label="female">女</el-radio>
                            </div>
                            <div>
                                <el-upload
                                class="avatar-uploader"
                                name='imageFile'
                                :data='postData'
                                action="http://localhost:3000/api/uploadAvatar"
                                :show-file-list="false"
                                :on-success="handleAvatarSuccess">
                                <img v-if="imageUrl || imageData" :src="imageData" class="avatar">
                                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                                </el-upload>
                            </div>
                    </div>
                </div>
            </template>
            <template slot='footer'>
                <el-button @click='updateInfo'>保存信息</el-button> 
                <br />
                <router-link :to="{path:'/'}">返回首页</router-link>
                   
            </template>
        </vaild-form>
    </div>
</template>
<script>
import vaildForm from '../components/vaildForm.vue'
import { api } from '../api.js'
import { mapState } from 'vuex'
import b64toBlob from 'b64-to-blob'
export default {
    name: 'reviseInfo',
    components:{
        vaildForm
    },
    data(){
        return{
            sex:'male',
            name:'loading',
            UID:'loading',
            createAt:'loading',
            avatar: '',
            admin: 'loading',
            imageUrl:'',
            imageData:'',
            postData:{
                username: ''
            },
        }
    },
    methods:{
        async getInfo(){
            let res = await api.post('http://localhost:3000/api/getUser', {username: this.username})
            this.name = res[0].name
            this.UID = res[0].ID
            this.createAt = res[0].createAt
            this.avatar = res[0].avatar
            this.sex = res[0].sex
            this.admin = res[0].admin
            this.postData.username = res[0].name
        },
        async getAvatar(){
            
            let res = await api.post('http://localhost:3000/api/getUserAvatar', {username: this.username})
            
            this.imageData = `data:image/jpeg;base64,${res}`
        }
        ,
        handleAvatarSuccess(res, file){
            console.log(file)
            this.imageUrl = URL.createObjectURL(file.raw)
            this.imageData = this.imageUrl
            this.$store.avatar = this.imageUrl
            sessionStorage.removeItem('avatar')
            sessionStorage.setItem('avatar', this.imageUrl)
            console.log(this.imageUrl)
        },
        async updateInfo(){
            let res = await api.post('http://localhost:3000/api/updateInfo', {sex:this.sex, username: this.username})
            console.log(res)
        }
    },
    created(){
        this.getInfo()
        this.getAvatar()
    },
    computed:mapState({
        username: state => state.username,
        avatar: state => state.avatar
    })
}
</script>
<style lang="scss" scoped>
.container{
    background:skyblue;
    width:100%;
    height:100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}
.sex_group{
    margin-top:20px;
}
.avatar-uploader .el-upload {
    border: 1px dashed #272727;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    border: 2px dashed #cecccc;
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
    margin-top:20px;
  }
  .avatar-uploader-icon:hover{
    border-color: #409EFF;
  }
  .avatar {
    margin-top:20px;
    border: 1px dashed #07ff13;
    width: 178px;
    height: 178px;
    display: block;
  }
</style>

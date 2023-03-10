<template>
    <div class="content">
        <a-card :style=" $breakpoints.xs ? 'margin-bottom: 30px' : 'margin-bottom: 30px; padding: 20px'">
            <span :style=" $breakpoints.md ? 'font-size: 25px;' : $breakpoints.xs ? 'font-size: 20px' : 'font-size: 36px'">Profile</span>
        </a-card>

        <a-card class="card-height" :style=" $breakpoints.sSm ? 'padding: 10px 0px' : 'padding: 30px'">
            <a-row type="flex" justify="end">
                <a-button @click="changePassButton">Change password</a-button>
            </a-row>
            <a-row type="flex" justify="center" :gutter="[20, 20]">
                <a-col :span="$breakpoints.md ? '24' : $breakpoints.sMd ? '24' : '18'">
                    <a-row type="flex" justify="center">
                        <a-col :span=" $breakpoints.xs ? '20' : $breakpoints.sSm ? '20' : $breakpoints.sm ? '18' : '24'">
                            <h1>Do you want to update your Profile? <span style="color: #1f67dc">Update here <a-icon type="arrow-down" /></span></h1>
                                <a-row :gutter="[10]">
                                    <a-form-model :model="form" :rules="rules" ref="ruleForm">
                                        <a-col :span="12">
                                            <a-form-model-item label="Firt Name" prop="fname" >
                                                <a-input v-model="form.fname"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="12">
                                            <a-form-model-item label="Middle Name">
                                                <a-input v-model="form.mname"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="12">
                                            <a-form-model-item label="Last Name" prop="lname">
                                                <a-input v-model="form.lname"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="12">
                                            <a-form-model-item label="Suffix">
                                                <a-input v-model="form.suffix"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                        <a-col :span="24">
                                            <a-form-model-item label="Subject" prop="subject">
                                                <a-input v-model="form.subject"></a-input>
                                            </a-form-model-item>
                                        </a-col>
                                    </a-form-model>
                                </a-row>
                            <a-row type="flex" justify="center">
                                <a-col :span="8">
                                    <a-button @click="saveProfile" block>Save</a-button>
                                </a-col>
                            </a-row>
                        </a-col>
                    </a-row>
                    
                </a-col>
            </a-row>
           
        </a-card>
        <change-password :visible="showModal" :form="passwordForm" @close="showModal = false" @submit="saveBtn"/>
    </div>
</template>

<script>
import Cookies from 'js-cookie';
export default {
    layout: 'header',
    data(){
        return{
            passwordForm: {},
            users: [],
            profInfo: [],
            form: {},
            rules: {
                fname: { required: true, message: 'Please input username', trigger: 'change'},
                lname: { required: true, message: 'Please input username', trigger: 'change'},
                subject: { required: true, message: 'Please input username', trigger: 'change'},
            },
            showModal: false

        }
    },
    async fetch(){
        await this.getUser();
    },
    methods:{
        async getUser(){
            try {
                let token = Cookies.get('token')
                console.log("token: >> ", token);
                let {data} = await this.$axios.get("/users/profile", { headers: { "token": token}})
                this.users = data
                let profData = await this.$axios.get(`/teachers/${data._id}`);
                this.profInfo = profData.data
                console.log("profData: >>", this.profInfo.profile.fname);
                console.log("data: >> ", data);
            } catch (error) {
                console.log(error);
            }
        },
        async saveProfile(){
            try {
                this.$refs.ruleForm.validate( async valid => {
                    if(valid){
                        // console.log(this.profInfo.profileId);
                        let {data} = await this.$axios.put(`/profiles/${this.profInfo.profileId}`, this.form) 
                        console.log(data);
                        let result = await this.$axios.put(`/teachers/${this.profInfo._id}`, this.form)
                        this.form = ""
                        this.$fetch();
                        this.$notification.success({
                            message: 'Success',
                            description: 'Successfully updated!'
                        })
                        console.log(result);
                        
                    } else {    
                        this.$notification.error({
                            message: 'Error',
                            description: 'Invalid'
                        })
                    }
                })
               
                // console.log("teacherInfo: >>", result.data );
            } catch (error) {
                console.log(error);
            }
        },
        changePassButton(){
            this.showModal = true;
        },
        async saveBtn(item){
            if(item.password != item.confirmPassword){
                this.$notification.error({
                    message: 'Error',
                    description: 'Password Doesn`t match!'
                })
            } else {
                item._id = this.users._id;
                let {data} = await this.$axios.put('/users/', item)
                if(data.message == 'Unauthorized'){
                    this.$notification.error({
                        message: "Error",
                        description: 'Unable to change your password'
                    })   
                    item.password = "";
                    item.confirmPassword = "";
                } else {
                    this.$notification.success({
                        message: "Success",
                        description: 'Successfully updated your password'
                    }) 
                    item.password = "";
                    item.confirmPassword = "";
                    this.showModal = false; 
                }
                
                console.log("data: >>", data);
                
            }
        }
    }
}
</script>

<style>

</style>
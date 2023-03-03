<template>
    <div class="body">
        <a-row type="flex" justify="center" >
            <div class="card-position" >
                <a-card class="card" >
                    <div class="card-title">
                        <h1>Register</h1>    
                    </div>
                    <div class="card-body">
                        <a-form-model ref="ruleForm" :rules="rules" :model="form">
                            <a-row gutter="10">
                                <a-col span="12">
                                    <a-form-model-item label="Frist name" prop="fname">
                                        <a-input v-model="form.fname"></a-input>
                                    </a-form-model-item>
                                </a-col>
                                <a-col span="12">
                                    <a-form-model-item label="Middle name" prop="mname">
                                        <a-input v-model="form.mname"></a-input>
                                    </a-form-model-item>
                                </a-col>
                                <a-col span="12">
                                    <a-form-model-item label="Last name" prop="lname">
                                        <a-input v-model="form.lname"></a-input>
                                    </a-form-model-item>
                                </a-col>
                                <a-col span="12">
                                    <a-form-model-item label="Suffix" prop="suffix">
                                        <a-input v-model="form.suffix"></a-input>
                                    </a-form-model-item>
                                </a-col>
                            </a-row>
                            <a-form-model-item label="Role" prop="role">
                                <a-select v-model="form.role">
                                    <a-select-option value="professor">Professor</a-select-option>
                                    <a-select-option value="student">Student</a-select-option>
                                </a-select>
                            </a-form-model-item>
                            <a-form-model-item label="Username" prop="username">
                                <a-input v-model="form.username"></a-input>
                            </a-form-model-item>
                            <a-form-model-item label="password" prop="password">
                                <a-input v-model="form.password"></a-input>
                            </a-form-model-item>
                            <a-form-model-item label="Confirm password" prop="cPassword">
                                <a-input v-model="form.cPassword"></a-input>
                            </a-form-model-item>  
                        </a-form-model>  
                    </div>
                    <div class="card-ftr">
                        <a-button type="primary" @click="register">Sign up</a-button>
                    </div>

                </a-card>
            </div>
        </a-row>
    </div>
</template>
  
<script>
import style from '../assets/css/style.css';
  export default {
    name: "IndexPage",
    data() {
        return {
            form: {},
            rules: {
                fname: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                lname: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                role: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                username: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                password: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                cPassword: { required: true, message: 'Please fill up this field!', trigger: 'change'},
            }
        };
    },
    methods: {
        register(){
            this.$refs.ruleForm.validate(valid => {
                if(valid){
                    if(this.form.cPassword != this.form.password){
                        this.$notification.error({
                            message: 'Error',
                            description: `Password doesn't match`
                        })
                    } else {
                        this.$notification.success({
                            message: 'Success',
                            description: 'Created account successfully'
                        })
                        this.$router.push('/')
                    }
                } else {
                    this.$notification.error({
                        message: 'Error',
                        description: 'Invalid'
                    })
                }
            })
        }
    },
}
</script>
<style>
.card{
    width: 650px;
    height: 65vh;
    /* display: inline-block; */
}
.ant-card-body{
    padding: 0;
}
.card-title{
    display: flex;
    justify-content: center;
    align-items: center;
    height: 80px;
}
.card-body{
    padding: 0 30px;
    height: 50vh;
    overflow-y: scroll;
}
.card-ftr{
    display: flex;
    padding: 20px 30px 0 30px;
    justify-content: center;
}

</style>

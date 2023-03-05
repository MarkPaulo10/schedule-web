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
                            <a-form-model-item label="Subject" v-if="form.role == 'professor'" prop="subject">
                                <a-input v-model="form.subject"></a-input>
                            </a-form-model-item>
                            <a-form-model-item label="Course" v-if="form.role == 'student'" prop="course">
                                <a-input v-model="form.course"></a-input>
                            </a-form-model-item>
                            <a-form-model-item label="Year" v-if="form.role == 'student'" prop="year">
                                <a-input v-model="form.year"></a-input>
                            </a-form-model-item>
                            <a-form-model-item label="Section" v-if="form.role == 'student'" prop="section">
                                <a-select v-model="form.section">
                                    <a-select-option value="A">A</a-select-option>
                                    <a-select-option value="B">B</a-select-option>
                                    <a-select-option value="C">C</a-select-option>
                                </a-select>
                            </a-form-model-item>
                            <a-form-model-item label="Username" prop="username">
                                <a-input v-model="form.username"></a-input>
                            </a-form-model-item>
                            <a-form-model-item label="email" prop="email">
                                <a-input v-model="form.email"></a-input>
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
                subject: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                course: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                year: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                section: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                username: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                email: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                password: { required: true, message: 'Please fill up this field!', trigger: 'change'},
                cPassword: { required: true, message: 'Please fill up this field!', trigger: 'change'},
            }
        };
    },
    methods: {
        async register(){
            this.$refs.ruleForm.validate( async valid => {
                if(valid){
                    if(this.form.cPassword != this.form.password){
                        
                        try {
                            this.$notification.error({
                                message: 'Error',
                                description: `Password doesn't match`
                            })
                           
                        } catch (error) {
                            
                        }
                    } else {
                       
                        let {data} = await this.$axios.post("/users/", this.form)
                        if(this.data == 'Username already Exist'){
                            this.$notification.error({
                                message: "Error",
                                description: 'Username Already exist!'
                            })
                        } else {
                            this.$notification.success({
                                message: 'Success',
                                description: 'Created account successfully'
                            })
                            if(this.form.role == 'professor'){
                                this.form.userId = data._id
                                let profdata = await this.$axios.post("/teachers/", this.form)
                                console.log("profData: >>", profdata.data);
                                this.form.teacherId = profdata.data._id;
                                profileResult = await this.$axios.post("/profiles/", this.form)
                                console.log("profResult:>>", profileResult);
                                this.$router.push('/')
                            } else {
                                this.form.userId = data._id;
                                let studentData = await this.$axios.post("/students/", this.form);
                                this.form.studentId = studentData.data._id;
                                studentResult = await this.$axios.post('/profiles', this.form);
                                console.log("studentResult", studentResult);
                                this.$router.push('/')
                            }
                        }
                        
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

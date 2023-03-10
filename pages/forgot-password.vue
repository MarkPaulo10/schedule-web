<template>
    <div class="body">
        <a-row type="flex" justify="center">
            <a-col>
                <div class="fp-card">
                    <a-card  title="Reset password" :style="$breakpoints.xs ? 'width: 100%; height: 400px' : 'width: 450px;'">
                        <a-row :gutter="[0,20]">
                            <a-col >
                                <a-form-model v-if="!verification">
                                    <a-form-model-item label="Email" prop="email">
                                        <a-input v-model="email"></a-input>
                                    </a-form-model-item>
                                </a-form-model>
                                <a-form-model v-else>
                                    <a-form-model-item label="Code" prop="code">
                                        <a-input v-model="verificationCode"></a-input>
                                    </a-form-model-item>
                                </a-form-model>
                            </a-col>
                            <a-col>
                                <div class="btn-footer" style="display: flex; justify-content: center; margin-bottom: 10px;">
                                    <a-button @click="!verification ? sendCode() : verificationBtn()">{{ !verification ?  "Send" : "Verify"}}</a-button>
                                </div>
                                
                                <a-row type="flex" justify="center">
                                    <p>Go to <a @click="$router.push('/')">Login</a></p>
                                </a-row>
                            </a-col>
                        </a-row>
                    </a-card>
                </div>
            </a-col>
        </a-row>
    </div>
</template>

<script>
import style from '../assets/css/style.css';
export default {
    data(){
        return{
            email: '',
            verification: false,
            verificationCode: '',
            rules: {
                
            }
        }
    },
    methods: {
        async sendCode(){
            try {
                console.log(this.email);
                if(!this.email ){
                    this.$notification.error({
                        message: 'Error',
                        description: 'Please fill up field',
                    })
                } else {                    
                    let {data} = await this.$axios.get(`/users/${this.email}`)
                    if(!data){
                        this.$notification.error({
                        message: 'Error',
                        description: 'Invalid Email!',
                    })
                    }else {
                        this.verification = true
                    }
                    
                }
                

                
            } catch (error) {
                console.log(error);
            }
        },
        async verificationBtn(){
            try {
                
              
                if(!this.verificationCode){
                    this.$notification.error({
                        message: 'Error',
                        description: 'Please fill up field!'
                    })
                }else {
                    let {data} = await this.$axios.post(`/users/reset`, {
                        email: this.email,
                        code: this.verificationCode,
                    })
                    if(data.message == "Unauthorized"){
                        this.$notification.error({
                            message: 'Error',
                            desciption: 'Invalid code'
                        })
                    } else{
                        console.log(data);
                        this.$notification.success({
                            message:"Success",
                            desciption: 'Successfully reset your password'
                        })
                        this.verification = false

                        this.$router.push("/")
                    }
                }
                
            } catch (error) {
                console.log(error);
            }
        }
    }
}
</script>

<style>
.fp-card{
    margin-top: 100px;
}
</style>
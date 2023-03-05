<template>
  <div class="body">
    <a-row type="flex" justify="center" >
      <div class="card-position">
        <a-card class="cards">
          <a-row type="flex" justify="center">
            <h1 class="title">Login</h1> 
          </a-row>
          <div class="form-body">
            <a-form-model :model="form" :rules="rules" ref="ruleForm">
              <a-form-model-item label="Username" prop="username">
                <a-input v-model="form.username" ></a-input>
              </a-form-model-item>
              <a-form-model-item label="Password" prop="password">
                <a-input v-model="form.password"></a-input>
              </a-form-model-item>
              <div class="login-btn">
                <a-button type="primary" block @click="login">Login</a-button>
              </div>
              <div style="margin-top: 20px;">
                <p>No account click here! <a @click="$router.push('/register')">Create account</a></p>
              </div>
            </a-form-model>
          </div>             
        </a-card>
      </div>
      
    </a-row>
  </div>
</template>

<script>
import Cookies from 'js-cookie';
import style from '../assets/css/style.css';
export default {
  name: 'IndexPage',
  data(){
    return{
      form: {},
      rules: {
        username: { required: true, message: 'Please input username', trigger: 'change'},
        password: { required: true, message: 'Please input password', trigger: 'change'}
      }
    }
  },
  methods:{
    async login(){
      this.$refs.ruleForm.validate( async valid => {
        if(valid){
          try {
            let {data} = await this.$axios.post("/users/login", this.form);
            console.log(data)
            
            if(data == "Unauthorized"){
              this.$notification.error({
                message: "Error",
                description: "Invalid"
              })
            } else {
              Cookies.set('token', data.token, {expires: 1})
              let token = Cookies.get('token')
              let result = await this.$axios.get("/users/profile", {headers: { 'token': token}})
               if(result.data.role == "professor"){
                  this.$notification.success({
                    message: "Success",
                    description: "Logged in successfully"
                  })
                  this.$router.push("/teacher")
                } else if( result.data.role == "student") {
                  this.$notification.success({
                    message: "Success",
                    description: "Logged in successfully"
                  })
                  this.$router.push("/student")
                } else {
                  this.$notification.error({
                    message: "Error",
                    description: "Invalid"
                  })
                }
            }
          } catch (error) {
            console.log(error);
          }
         
        } else {
          this.$notification.error({
            message: "Error",
            description: "Invalid"
          })
          console.log('Invalid')
        }
      })
     
    }
  }
}
</script>
<style>

</style>

<template>
    <div class="content">
        <a-card class="card-height">
            <div class='information'>
                <h1>Name: {{ `${students.profile&&students.profile.fname} ${students.profile&&students.profile.lname}`}}</h1>
                <h1>Course Y&S: {{ `${students.course} ${students.year}${students.section}` }}</h1>
                
            </div>
           
        </a-card>
       
    </div>
</template>

<script>
import Cookies from 'js-cookie';
export default {
    layout: 'header',
    data(){
        return{
            students: [],
        }
    },
    async fetch(){
        await this.getStudent();
    },
    methods: {
        async getStudent(){
           try {
             let token = Cookies.get('token');
             let {data} = await this.$axios.get("/users/profile", { headers: { "token": token}});
             let studentData = await this.$axios.get(`/students/${data._id}`);

             this.students = studentData.data
           } catch (error) {
            console.log(error);
           }
            // let {data} 
        }

    }
}
</script>

<style>
.content{
    padding: 50px;
    
}
.card-height{
    height: 60vh;
}
</style>
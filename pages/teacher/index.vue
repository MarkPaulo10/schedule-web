<template>
    <div class="content">
        <a-card class="card-height">
            <div class='information'>
                <h1>Name: {{ `${profInfo.profile&&profInfo.profile.fname} ${profInfo.profile&&profInfo.profile.lname}`}}</h1>
                <h1>Teacher subject:{{ ` ${profInfo.subject}`}}</h1>
            </div>
           
        </a-card>
       
    </div>
</template>

<script>
import Cookies from 'js-cookie';
export default {
    layout: 'main',
    data(){
        return{
            profInfo: [],

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
                let profData = await this.$axios.get(`/teachers/${data._id}`);
                this.profInfo = profData.data
                console.log("profData: >>", this.profInfo.profile.fname);
                console.log("data: >> ", data);
            } catch (error) {
                console.log(error);
            }
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